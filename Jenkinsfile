pipeline {
    agent any

    environment {
        VENV_DIR = 'anime'
        GCP_PROJECT = 'coral-core-466607-r4'
        GCLOUD_PATH = "/var/jenkins_home/google-cloud-sdk/bin"
        GCP_REGION = "us-central1"
        CLUSTER_NAME = "animecluster"
    }

    stages {

        stage("Clone Repo") {
            steps {
                echo "Cloning from GitHub"
                checkout scmGit(
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        credentialsId: 'anime_github_token',
                        url: 'https://github.com/prash60403/Anime_Recommendation_.git'
                    ]]
                )
            }
        }

        stage("Create Virtual Environment") {
            steps {
                sh '''
                    # Remove old broken venv if exists
                    rm -rf ${VENV_DIR}

                    # Create fresh venv
                    python3 -m venv ${VENV_DIR}
                    . ${VENV_DIR}/bin/activate

                    pip install --upgrade pip
                    pip install -r requirements.txt
                    pip install dvc
                '''
            }
        }

        stage("DVC Pull") {
            steps {
                withCredentials([file(
                    credentialsId: 'gcp_key',
                    variable: 'GOOGLE_APPLICATION_CREDENTIALS'
                )]) {
                    sh '''
                        . ${VENV_DIR}/bin/activate
                        export PATH=$PATH:${GCLOUD_PATH}
                        dvc pull
                    '''
                }
            }
        }

        stage("Build and Push Image to GCR") {
            steps {
                withCredentials([file(
                    credentialsId: 'gcp_key',
                    variable: 'GOOGLE_APPLICATION_CREDENTIALS'
                )]) {
                    sh '''
                        export PATH=$PATH:${GCLOUD_PATH}

                        gcloud auth activate-service-account --key-file=${GOOGLE_APPLICATION_CREDENTIALS}
                        gcloud config set project ${GCP_PROJECT}

                        gcloud auth configure-docker gcr.io --quiet

                        docker build -t gcr.io/${GCP_PROJECT}/ml-project:latest .
                        docker push gcr.io/${GCP_PROJECT}/ml-project:latest
                    '''
                }
            }
        }

        stage("Deploying to Kubernetes") {
            steps {
                withCredentials([file(
                    credentialsId: 'gcp_key',
                    variable: 'GOOGLE_APPLICATION_CREDENTIALS'
                )]) {
                    sh '''
                        export PATH=$PATH:${GCLOUD_PATH}

                        gcloud auth activate-service-account --key-file=${GOOGLE_APPLICATION_CREDENTIALS}
                        gcloud config set project ${GCP_PROJECT}

                        gcloud container clusters get-credentials ${CLUSTER_NAME} --region ${GCP_REGION}

                        kubectl apply -f deployment.yaml
                    '''
                }
            }
        }
    }
}





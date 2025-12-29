pipeline {
    agent any

    environment {
        VENV_DIR = 'venv'
        GCP_PROJECT= 'animerecommendation-482717'
        GCLOUD_PATH= "/var/jenkins_home/google-cloud-sdk/bin"
        KUBECTL_AUTH_PLUGIN=   "/user/lib/google-cloud-sdk/bin"

    }

    stages {

        stage("Clone Repo") {
            steps {
                echo "Cloning from GitHub"
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'anime_github_token', url: 'https://github.com/prash60403/Anime_Recommendation_.git']])
            }
        }

        stage("Create Virtual Environment") {
            steps {
                sh '''
                    python3 -m venv ${VENV_DIR}
                    . ${VENV_DIR}/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                    pip install dvc
                '''
            }
        }

        stage('DVC Pull'){
            steps{
                withCredentials([file(credentialsId:'gcp_key' , variable: 'GOOGLE_APPLICATION_CREDENTIALS' )]){
                    script{
                        echo 'DVC Pul....'
                        sh '''
                        . ${VENV_DIR}/bin/activate
                        dvc pull
                        '''
                    }
                }
            }
        }


        stage('Build and Push Image to GCR'){
            steps{
                withCredentials([file(credentialsId:'gcp_key' , variable: 'GOOGLE_APPLICATION_CREDENTIALS' )]){
                    script{
                        echo 'Build and Push Image to GCR'
                        sh '''
                        export PATH=$PATH:${GCLOUD_PATH}
                        gcloud auth activate-service-account --key-file=${GOOGLE_APPLICATION_CREDENTIALS}
                        gcloud config set project ${GCP_PROJECT}
                        gcloud auth configure-docker --quiet
                        docker build -t gcr.io/${GCP_PROJECT}/ml-project:latest .
                        docker push gcr.io/${GCP_PROJECT}/ml-project:latest
                        '''
                    }
                }
            }
        }


        stage('Deploying to Kubernetes'){
            steps{
                withCredentials([file(credentialsId:'gcp_key' , variable: 'GOOGLE_APPLICATION_CREDENTIALS' )]){
                    script{
                        echo 'Deploying to Kubernetes'
                        sh '''
                        export PATH=$PATH:${GCLOUD_PATH}:${KUBECTL_AUTH_PLUGIN}
                        gcloud auth activate-service-account --key-file=${GOOGLE_APPLICATION_CREDENTIALS}
                        gcloud config set project ${GCP_PROJECT}
                        gcloud container clusters get-credentials animecluster --region us-central1
                        kubectl apply -f deployment.yaml
                        '''
                    }
                }
            }
        }
        }
    }




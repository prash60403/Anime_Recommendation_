pipeline {
    agent any

    environment {
        VENV_DIR = 'venv'
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

        stage("DVC Pull") {
            steps {
                withCredentials([file(credentialsId: 'gcp_key', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                    sh '''
                        . ${VENV_DIR}/bin/activate
                        dvc pull
                    '''
                }
            }
        }

    }
}

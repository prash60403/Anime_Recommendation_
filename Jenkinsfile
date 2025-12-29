pipeline{
    agent any
    environment{
        VENV_DIR= 'venv'
    }
    stages{
        stage("cloning from github..."){

        steps{
            script{
                echo "Cloniing from github"
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'anime_github_token', url: 'https://github.com/prash60403/Anime_Recommendation_.git']])
            }
        }
    }
    stage("Making  virtual environment ..........."){

        steps{
            script{
                echo "Making  virtual environment ..........."
                sh '''
                python -m venv ${VENV_DIR}
                .${VENV_DIR}/bin/activate
                pip install --upgrade pip
                pip install -e
                pip install dvc
                '''
            }
        }
    }
    stage('DVC Pull'){
        steps{
            withCredentials([file(credentialsId: 'gcp_key', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]){
                script{
                    echo "DVC Pull"
                    sh '''
                    .${VENV_DIR}/bin/activate
                    dvc pull
                    '''
                }
            }
        }
    }
}
}
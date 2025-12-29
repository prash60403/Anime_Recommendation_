pipeline{
    agent any
    stages{
        stage("cloning from github..."){

        steps{
            script{
                echo "Cloniing from github"
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'anime_github_token', url: 'https://github.com/prash60403/Anime_Recommendation_.git']])
            }
        }
    }
}
}
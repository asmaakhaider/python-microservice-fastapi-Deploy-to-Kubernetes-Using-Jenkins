pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', 
                    branches: [[name: '*/main']], 
                    extensions: [], 
                    userRemoteConfigs: [[url: 'https://github.com/asmaakhaider/python-microservice-fastapi-Deploy-to-Kubernetes-Using-Jenkins']]
                ])
                // Lister les fichiers après le checkout pour s'assurer que le code est bien récupéré
                sh 'ls -l'
            }
        }

        stage('Build Docker Image for movie-service') {
            steps {
                script {
                    sh 'docker build -t assma123/movie-service:${BUILD_NUMBER} ./movie-service'
                }
            }
        }

        stage('Build Docker Image for cast-service') {
            steps {
                script {
                    sh 'docker build -t assma123/cast-service:${BUILD_NUMBER} ./cast-service'
                }
            }
        }
        stage('Push Image for movie-service to Hub') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'dockerhub_pwd')]) {
                        sh 'echo $dockerhub_pwd | docker login -u assma123 --password-stdin'
                    }
                    sh 'docker push assma123/movie-service:${BUILD_NUMBER}'
                }
            }
        }

        stage('Push Image for cast-service to Hub') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'dockerhub_pwd')]) {
                        sh 'echo $dockerhub_pwd | docker login -u assma123 --password-stdin'
                    }
                    sh 'docker push assma123/cast-service:${BUILD_NUMBER}'
                }
            }
        }
        stage('Docker run') { 
            steps {
                script {
                    sh '''
                    docker run -d -p 8001:8000 assma123/movie-service:${BUILD_NUMBER}
                    docker run -d -p 8002:8000 assma123/cast-service:${BUILD_NUMBER}
                    sleep 10
                    docker ps
                    '''
                }
            }
        }
    }
}

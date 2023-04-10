pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t minha_imagem:latest .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD'
                }

                sh 'docker push minha_imagem:latest'
            }
        }
        
        stage('Push Docker Image to Private Registry') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'private-registry-cred', usernameVariable: 'REGISTRY_USERNAME', passwordVariable: 'REGISTRY_PASSWORD')]) {
                    sh 'docker login -u $REGISTRY_USERNAME -p $REGISTRY_PASSWORD minha-registry.com'
                }
                sh 'docker tag minha_imagem:latest minha-registry.com/minha_imagem:latest'
                sh 'docker push minha-registry.com/minha_imagem:latest'
            }
        }
               
        stage('Deploy Docker Image') {
            when {
                branch 'main'
            }

            steps {
                sh 'docker stop minha_aplicacao || true'
                sh 'docker rm minha_aplicacao || true'
                sh 'docker run -d --name minha_aplicacao -p 80:80 minha_imagem:latest'
            }
        }
    }
}


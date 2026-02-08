pipeline {
    agent any

    environment {
        ACR_NAME = "mydevopsacr"
        IMAGE_NAME = "cloud-web-app"
        IMAGE_TAG = "latest"
        ACR_LOGIN_SERVER = "mydevopsacr.azurecr.io"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/zoya9545-web/cloud-web-app.git' 
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                docker build -t $ACR_LOGIN_SERVER/$IMAGE_NAME:$IMAGE_TAG .
                """
            }
        }

        stage('Login to ACR') {
            steps {
                sh """
                az acr login --name $ACR_NAME
                """
            }
        }

        stage('Push Image to ACR') {
            steps {
                sh """
                docker push $ACR_LOGIN_SERVER/$IMAGE_NAME:$IMAGE_TAG
                """
            }
        }

        stage('Deploy to AKS') {
            steps {
                sh """
                kubectl apply -f k8s/deployment.yaml
                kubectl apply -f k8s/service.yaml
                """
            }
        }
    }
}

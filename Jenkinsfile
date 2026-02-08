pipeline {
    agent any

    environment {
        ACR_NAME = "mydevopsacr"
        ACR_LOGIN_SERVER = "mydevopsacr.azurecr.io"
        IMAGE_NAME = "cloud-web-app"
        IMAGE_TAG = "latest"
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

        stage('Login to Azure Container Registry') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'acr-creds',
                    usernameVariable: 'ACR_USER',
                    passwordVariable: 'ACR_PASS'
                )]) {
                    sh """
                    docker login $ACR_LOGIN_SERVER \
                    -u $ACR_USER \
                    -p $ACR_PASS
                    """
                }
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

    post {
        success {
            echo "✅ Pipeline completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed. Check logs."
        }
    }
}

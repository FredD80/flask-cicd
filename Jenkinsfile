pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-creds')
        IMAGE_NAME = "fredd1/flaskapp"
    }
    stages {
        stage('Setup Buildx') {
            steps {
                script {
                    // Initialize Buildx builder with Docker driver
                    sh 'docker buildx create --use --name mybuilder --driver docker'
                    sh 'docker buildx inspect --bootstrap'
                }
            }
        }
        
        stage('Build Image') {
            steps {
                script {
                    // Build and load image into local Docker daemon
                    sh """
                    docker buildx build \
                        --platform linux/amd64 \
                        --load \
                        -t ${IMAGE_NAME}:${BUILD_NUMBER} \
                        -t ${IMAGE_NAME}:latest \
                        .
                    """
                }
            }
        }
        
        stage('Login to Docker Hub') {
            steps {
                script {
                    // Authenticate before pushing
                    sh "echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin"
                }
            }
        }
        
        stage('Push Image') {
            steps {
                script {
                    // Push both versioned and latest tags
                    sh """
                    docker push ${IMAGE_NAME}:${BUILD_NUMBER}
                    docker push ${IMAGE_NAME}:latest
                    """
                }
            }
        }
    }
    post {
        always {
            sh 'docker logout'
            sh 'docker buildx rm mybuilder || true'  // Cleanup builder
        }
    }
}

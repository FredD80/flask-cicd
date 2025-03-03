pipeline {
    agent { docker { image 'docker:latest' } }
    environment {
        DH_ACT = 'fredd1' // Your Docker Hub username (renamed variable)
        // ... other environment variables
        IMAGE_NAME = 'flaskapp' // Your image name without the username
    }
    stages {
        stage('Build Image') {
            steps {
                script {
                    sh """
                        docker buildx create --name mybuilder --driver docker-container --use
                        docker buildx inspect --bootstrap
                        docker buildx build \
                            --platform linux/amd64,linux/arm64 \
                            --load \
                            -t ${DH_ACT}/${IMAGE_NAME}:${BUILD_NUMBER} \
                            -t ${DH_ACT}/${IMAGE_NAME}:latest \
                            .
                    """
                }
            }
        }
        stage('Login to Docker Hub') {
            steps {
                script {
                    sh "echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DH_ACT} --password-stdin" // Use DH_ACT here
                }
            }
        }
        stage('Push Image') {
            steps {
                script {
                    sh """
                        docker push ${DH_ACT}/${IMAGE_NAME}:${BUILD_NUMBER}
                        docker push ${DH_ACT}/${IMAGE_NAME}:latest
                    """
                }
            }
        }
    }
    post {
        always {
            sh 'docker logout'
            sh 'docker buildx rm mybuilder || true'
        }
    }
}
~
~

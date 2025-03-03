pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-creds')
        IMAGE_NAME = "fredd1/flaskapp"
    }
    stages {
        stage('Build and Push Docker Image') {
            steps {
                script {
                    // First, build the image using the legacy builder if necessary
                    sh "docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} ."

                    // Then, use buildx to push the image (buildx is better at pushing multi-platform images)
                    sh """
                    docker buildx build \
                        --platform linux/amd64 \
                        --push \
                        -t ${IMAGE_NAME}:${BUILD_NUMBER} \
                        .
                    """
                }
            }
        }
    }
    post {
        always {
            sh 'docker logout'
        }
    }
}

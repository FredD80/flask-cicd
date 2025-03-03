pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-creds')
        IMAGE_NAME = "fredd1/flaskapp" // Corrected Docker Hub repository name
    }
    stages {
        stage('Build Docker Image') {
            steps {
                script {
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
        stage('login to dockerhub') {
            steps {
                script {
                    sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
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

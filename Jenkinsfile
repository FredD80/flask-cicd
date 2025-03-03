pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-creds')
        IMAGE_NAME = "flaskapp"
        DH_ACT = "fredd1"
    }
    stages {
        stage('Setup Buildx') {
            steps {
                script {
                    sh 'docker buildx rm mybuilder || true'
                    sh 'docker buildx create --use --name mybuilder --driver docker-container'
                    sh 'docker buildx inspect --bootstrap'
                }
            }
        }

        stage('Build Image') {
            steps {
                script {
                    sh """
                    docker buildx build \\
                        --platform linux/amd64 \\
                        --load \\
                        -t ${DH_ACT}/${IMAGE_NAME}:${BUILD_NUMBER} \\
                        -t ${DH_ACT}/${IMAGE_NAME}:latest \\
                        .
                    """
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    sh "echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin"
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

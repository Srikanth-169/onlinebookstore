pipeline {
    agent { label 'tes' }

    environment {
        APP_NAME = "online-book-store"
        IMAGE_NAME = "online-book-store:v1"
        CONTAINER_NAME = "bookstore"
        HOST_PORT = "8008"
        CONTAINER_PORT = "8080"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/Srikanth-169/onlinebookstore.git'
            }
        }

        stage('Check Workspace') {
            steps {
                sh 'ls -R'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker rmi -f ${IMAGE_NAME} || true"
                sh "docker build -t ${IMAGE_NAME} -f Dockerfile ."
            }
        }

        stage('Deploy Container') {
            steps {
                sh "docker rm -f ${CONTAINER_NAME} || true"
                sh "docker run -d --name ${CONTAINER_NAME} -p ${HOST_PORT}:${CONTAINER_PORT} ${IMAGE_NAME}"
            }
        }

        stage('Check Deployment') {
            steps {
                sh "docker ps | grep ${CONTAINER_NAME}"
            }
        }
    }

    post {
        failure {
            echo "❌ Deployment failed. Check logs."
        }
        success {
            echo "✅ Deployment successful."
        }
    }
}

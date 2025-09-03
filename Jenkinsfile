pipeline {
    agent any

    environment {
        APP_NAME = "online-book-store"
        IMAGE_NAME = "online-book-store:v1"
        CONTAINER_NAME = "bookstore"
        HOST_PORT = "8083"
        CONTAINER_PORT = "8080"
    }

    stages {
        stage('Checkout Git') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/Srikanth-169/onlinebookstore.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                  docker rmi -f IMAGE_NAME || true
                sh 'docker build -t online-book-store:v1 -f docker/Dockerfile .'
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                  docker rm -f CONTAINER_NAME || true
                  docker run -d --name CONTAINER_NAME -p HOST_PORT:CONTAINER_PORT IMAGE_NAME
                '''
            }
        }

        stage('Check Deployment') {
            steps {
                sh 'docker ps | grep CONTAINER_NAME || true'
            }
        }
    }

    post {
        success {
            echo "✅ Online Book Store deployed successfully at http://<server-ip>:HOST_PORT"
        }
        failure {
            echo "❌ Deployment failed. Check logs."
        }
    }
}

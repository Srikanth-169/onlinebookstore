pipeline {
    agent any

    environment {
        APP_NAME = "online-book-store"
        IMAGE_NAME = "online-book-store:v1"
        CONTAINER_NAME = "bookstore"
        HOST_PORT = "9090"
        CONTAINER_PORT = "8080"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/your-username/online-book-store.git'
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
                  docker rmi -f $IMAGE_NAME || true
                  docker build -t $IMAGE_NAME -f Dockerfile .
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                  # Remove old container if exists
                  docker rm -f $CONTAINER_NAME || true

                  # Run new container
                  docker run -d --name $CONTAINER_NAME -p $HOST_PORT:$CONTAINER_PORT $IMAGE_NAME
                '''
            }
        }

        stage('Check Deployment') {
            steps {
                sh 'docker ps | grep $CONTAINER_NAME'
            }
        }
    }

    post {
        success {
            echo "✅ Online Book Store deployed successfully at http://<server-ip>:$HOST_PORT"
          }
        }
    }
}

pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "online-book-store:v1"
        APP_NAME = "online-book-store"
        SERVER = "your.server.ip.here"   // Replace with your server IP
        SERVER_USER = "youruser"         // Replace with your server's SSH user
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/Srikanth-169/onlinebookstore.git'
            }
        }

        stage('Check Workspace') {
            steps {
                sh 'ls -R'
            }
        }

       stage('Build Docker Image') {
          steps {
            sh "docker build -t ${DOCKER_IMAGE} ."
            }
         }


        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push ${DOCKER_IMAGE}
                    '''
                }
            }
        }

        stage('Deploy Container') {
            steps {
                sshagent(['ssh-key-id']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ${SERVER_USER}@${SERVER} "
                        docker pull ${DOCKER_IMAGE}
                        docker stop ${APP_NAME} || true
                        docker rm ${APP_NAME} || true
                        docker run -d --name ${APP_NAME} -p 9090:8080 ${DOCKER_IMAGE}
                    "
                    '''
                }
            }
        }

        stage('Check Deployment') {
            steps {
                sh 'docker ps'
            }
        }
    }

    post {
        always {
            echo '✅ Pipeline completed.'
        }
        failure {
            echo '❌ Deployment failed. Check logs.'
        }
    }
}

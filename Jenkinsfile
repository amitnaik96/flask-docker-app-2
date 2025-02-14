pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'darkxprime/flask-app'
        DOCKER_CREDENTIALS = 'docker-hub-credentials'  // Stored in Jenkins
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/amitnaik96/flask-docker-app-2.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %DOCKER_IMAGE% .'
            }
        }

        stage('Run Tests') {
            steps {
                bat 'docker run --rm -e PYTHONPATH=/app %DOCKER_IMAGE% pytest tests/'
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: DOCKER_CREDENTIALS, url: '']) {
                    bat 'docker push %DOCKER_IMAGE%'
                }
            }
        }

        stage('Deploy to remote server') {
            steps {
                echo "Deployed Application!"
                // Uncomment for remote deployment via SSH
                // sshagent(credentials: [SSH_CREDENTIALS]) {
                //     bat '''
                //     ssh -o StrictHostKeyChecking=no ubuntu@%AWS_HOST% <<EOF
                //     docker pull %DOCKER_IMAGE%
                //     docker stop flask_app || echo "Container not running"
                //     docker rm flask_app || echo "No container to remove"
                //     docker run -d -p 5000:5000 --name flask_app %DOCKER_IMAGE%
                //     EOF
                //     '''
                // }
            }
        }
    }

    post {
        success {
            echo "✅ Success!"
        }
        failure {
            echo "❌ Failed!"
        }
    }
}

pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'darkxprime/flask-app'
        DOCKER_CREDENTIALS = 'docker-hub-credentials'  // Stored in Jenkins
        CODESPACE_HOST = 'your-codespace-id.github.dev'
        CODESPACE_PORT = '2222'
        SSH_CREDENTIALS = 'github-codespaces-ssh'  // Stored in Jenkins
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
                bat 'docker run --rm %DOCKER_IMAGE% pytest tests/'
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: DOCKER_CREDENTIALS, url: '']) {
                    bat 'docker login -u your-dockerhub-username -p your-password'
                    bat 'docker push %DOCKER_IMAGE%'
                }
            }
        }

        stage('Deploy Application to GitHub Codespaces') {
            steps {
                sshagent(credentials: [SSH_CREDENTIALS]) {
                    bat '''
                    ssh -p %CODESPACE_PORT% -o StrictHostKeyChecking=no codespace@%CODESPACE_HOST% <<EOF
                    docker pull %DOCKER_IMAGE%
                    docker stop flask_app || echo "Container not running"
                    docker rm flask_app || echo "No container to remove"
                    docker run -d -p 5000:5000 --name flask_app %DOCKER_IMAGE%
                    EOF
                    '''
                }
            }
        }
    }

    post {
        success {
            echo "✅ Deployment Successful!"
        }
        failure {
            echo "❌ Deployment Failed!"
        }
    }
}

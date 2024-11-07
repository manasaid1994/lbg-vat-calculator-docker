pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'manasaid1994/calc-sonarqube-run'  // Docker image name
        DOCKER_CREDENTIALS = 'DOCKER_LOGIN'         // Docker Hub credentials ID
        SSH_CREDENTIALS = 'jenkins'            // SSH credentials ID
        TARGET_HOST = '35.210.245.45'                     // IP address of target machine
    }

    stages {
        stage('Checkout') {
            steps {
                 git branch: 'main', url: 'https://github.com/manasaid1994/lbg-vat-calculator-docker.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    sh 'docker build -t $DOCKER_IMAGE .'
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    // Log in to Docker Hub and push the image
                    docker.withRegistry('', DOCKER_CREDENTIALS) {
                        sh 'docker push $DOCKER_IMAGE'
                    }
                }
            }
        }

        stage('Deploy on Remote Machine') {
            steps {
                script {
                    // SSH into the target machine and run Docker commands
                    sshagent(credentials: [SSH_CREDENTIALS]) {
                        sh """
                            # Pull the latest Docker image from Docker Hub
                            ssh -o StrictHostKeyChecking=no $SSH_CREDENTIALS@$TARGET_HOST "
                                docker pull $DOCKER_IMAGE && \
                                docker run -d -p 3000:3000 --name app-container $DOCKER_IMAGE
                            "
                        """
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Docker image successfully pushed and deployed."
        }
        failure {
            echo "Build or deployment failed."
        }
    }
}

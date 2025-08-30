pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "thedevopsdocker/todo-app:1.0"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Shubhan-siri/New-todo-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    script {
                        retry(3) { // Retry 3 times if push fails
                            timeout(time: 5, unit: 'MINUTES') { // Maximum 5 minutes
                                sh '''
                                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                                    docker push $DOCKER_IMAGE
                                '''
                            }
                        }
                    }
                }
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                    docker rm -f todo-container || true
                    docker run -d -p 5000:5000 --name todo-container $DOCKER_IMAGE
                '''
            }
        }
    }

    post {
        failure {
            echo "Pipeline failed. Please check logs."
        }
        success {
            echo "Pipeline completed successfully!"
        }
    }
}

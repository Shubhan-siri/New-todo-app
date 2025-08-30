pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = "your-dockerhub-username/todo-app:latest"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/<your-username>/todo-app.git'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push $DOCKER_IMAGE
                    '''
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
}

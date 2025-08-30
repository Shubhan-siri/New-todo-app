pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = "thedevopsdocker/todo-app:1.0"
    }

    stages {
        stage('Checkout') {
            steps {
                // Explicitly main branch ka use kar rahe hain
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
                    credentialsId: 'dockerhub',       // Jenkins me jo ID di thi
                    usernameVariable: 'DOCKER_USER',  // bas variable ka naam
                    passwordVariable: 'DOCKER_PASS'   // yaha bhi bas naam
                )]) {
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

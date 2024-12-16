pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials(sjangx7) 
        DOCKER_IMAGE_NAME = sjangx7/cw2-server:1.0 
    }
    stages {
        stage('Checkout') {
            steps {
                echo 'Cloning repository...'
                git url: https://github.com/sjangx7/CW2.git, branch: 'main'
            }
        }
        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                script {
                    sh 'docker build -t $DOCKER_IMAGE_NAME .'
                }
            }
        }
        stage('Test Container Launch') {
            steps {
                echo 'Testing container launch...'
                script {
                    // Run the container in detached mode
                    sh 'docker run -d --name test-container $DOCKER_IMAGE_NAME'
                    // Execute a simple command inside the container to confirm it's running
                    sh 'docker exec test-container echo "Container is running"'
                    // Stop and remove the test container
                    sh 'docker stop test-container'
                    sh 'docker rm test-container'
                }
            }

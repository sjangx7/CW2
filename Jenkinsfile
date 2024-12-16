pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'your-dockerhub-username/cw2-server'
        DOCKER_TAG = 'latest'
        K8S_DEPLOYMENT_NAME = 'cw2-server-deployment'
        K8S_NAMESPACE = 'default'
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Checkout code from GitHub
                git credentialsId: 'github-credentials', branch: 'main', url: 'https://github.com/sjangx7/CW2.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image using Dockerfile
                    docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                }
            }
        }

        stage('Test Docker Image') {
            steps {
                script {
                    // Run the container and test if it launches successfully
                    def image = docker.image("${DOCKER_IMAGE}:${DOCKER_TAG}")
                    image.inside {
                        sh 'echo "Testing container launch"'
                        sh 'curl http://localhost' // Example test command
                    }
                }
            }
        }

        stage('Push Docker Image to DockerHub') {
            steps {
                script {
                    // Push the Docker image to DockerHub
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials') {
                        docker.image("${DOCKER_IMAGE}:${DOCKER_TAG}").push()
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Set kubeconfig if not already set
                    withKubeConfig(credentialsId: 'kubeconfig') {
                        // Deploy or update the deployment in Kubernetes
                        sh """
                        kubectl set image deployment/${K8S_DEPLOYMENT_NAME} ${K8S_DEPLOYMENT_NAME}=${DOCKER_IMAGE}:${DOCKER_TAG} --namespace=${K8S_NAMESPACE}
                        kubectl rollout status deployment/${K8S_DEPLOYMENT_NAME} --namespace=${K8S_NAMESPACE}
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()  // Clean workspace after the pipeline runs
        }
    }
}

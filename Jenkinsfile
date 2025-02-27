pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                sh 'echo "Cloning GitHub project..."'
                git branch: 'main', credentialsId: 'jenkins-ssh-key', url: 'git@github.com:piyush-droid-cloud/CI-CD-pipeline.git'
            }
        }

        stage('Build Docker image') {
            steps {
                sh 'echo "Checking if Docker is running..."'
                sh 'docker info || exit 1'

                sh 'echo "Building Docker Image..."'
                sh 'docker build -t badiyaricloud04/ci-cd-project:latest .'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub-credentials', url: 'https://index.docker.io/v1/']) {
                    sh 'echo "Successfully logged into Docker Hub"'
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub-credentials', url: 'https://index.docker.io/v1/']) {
                    sh 'echo "Pushing Docker Image..."'
                    sh 'docker push badiyaricloud04/ci-cd-project:latest'
                }
            }
        }

        stage('Deploy Application') {
            steps {
                sh 'echo "Stopping old container if exists..."'
                sh 'docker stop my-app || true && docker rm my-app || true'

                sh 'echo "Deploying new container..."'
                sh 'docker run -d --name my-app -p 5000:5000 badiyaricloud04/ci-cd-project:latest'
            }
        }
    }
}


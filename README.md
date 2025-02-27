**📌 CI/CD Pipeline using Jenkins and GitHub**
1️⃣ Project Overview
This project automates the software development lifecycle (SDLC) using a CI/CD pipeline with Jenkins, GitHub, and Docker.

Continuous Integration (CI): Automatically builds and tests code changes.
Continuous Deployment (CD): Deploys the application to a containerized environment.

2️⃣ Technology Stack
Component	Description
Jenkins	Automates the pipeline execution
GitHub	Stores the application code & Jenkinsfile
Docker	Builds & runs the application in a container
Flask (Python)	Sample web application
Docker Hub	Stores the built Docker images

3️⃣ Step-by-Step Implementation
🟢 Step 1: Setup Jenkins on Ubuntu
🔹 Install Java (Required for Jenkins)
**bash**
sudo apt update && sudo apt upgrade -y
sudo apt install openjdk-11-jdk -y
java -version
🔹 Install Jenkins
**bash**
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update
sudo apt install jenkins -y
🔹 Start & Enable Jenkins
**bash**
sudo systemctl start jenkins
sudo systemctl enable jenkins
🔹 Access Jenkins
Open your browser and go to http://YOUR_SERVER_IP:8080
Find the Admin password using:
**bash**
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
Install suggested plugins and create an admin user.

🟢 Step 2: Configure SSH Access for GitHub in Jenkins
🔹 Generate SSH Key (If Not Created)
**bash**
sudo -u jenkins ssh-keygen -t rsa -b 4096 -C "your-email@example.com"
🔹 Copy Public Key to GitHub
**bash**
cat /var/lib/jenkins/.ssh/id_rsa.pub
Add this key to GitHub → Settings → SSH and GPG keys.
🔹 Test SSH Connection
**bash**
sudo -u jenkins ssh -T git@github.com
✅ If successful, you should see:
Hi piyush-droid-cloud! You've successfully authenticated.

🟢 Step 3: Clone the GitHub Repository in Jenkins Server
**bash**
sudo -u jenkins git clone git@github.com:piyush-droid-cloud/CI-CD-pipeline.git

🟢 Step 4: Create the Application (app.py)
🔹 Create a simple Flask web app
**python**
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return "Hello, CI/CD Pipeline with Jenkins!"

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
    
🟢 Step 5: Create requirements.txt
**plaintext**
flask

🟢 Step 6: Create the Dockerfile
**dockerfile**
# Use official Python image
FROM python:3.9

# Set working directory
WORKDIR /app

# Copy application files
COPY . /app

# Install dependencies
RUN pip install -r requirements.txt

# Run the app
CMD ["python", "app.py"]

🟢 Step 7: Create the Jenkinsfile
**groovy**
pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', credentialsId: 'jenkins-ssh-key', url: 'git@github.com:piyush-droid-cloud/CI-CD-pipeline.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t piyush-droid-cloud/ci-cd-project:latest .'
            }
        }
        stage('Push Image to Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'docker-hub-credentials', url: '']) {
                    sh 'docker push piyush-droid-cloud/ci-cd-project:latest'
                }
            }
        }
        stage('Deploy Application') {
            steps {
                sh 'docker run -d -p 5000:5000 piyush-droid-cloud/ci-cd-project:latest'
            }
        }
    }
}

🟢 Step 8: Commit and Push All Files
**bash**
git add .
git commit -m "Added CI/CD pipeline with Jenkins"
git push origin main

🟢 Step 9: Configure Jenkins Job
Create a New Item → Pipeline
Select "Pipeline script from SCM"
Set Repository URL: git@github.com:piyush-droid-cloud/CI-CD-pipeline.git
Set Branch: main
Set Credentials ID: jenkins-ssh-key
Click "Build Now"

pipeline {
    agent any

    environment {
        DOCKERHUB = credentials('dockerhub-creds')
        IMAGE_NAME = "nourjbeli123/student-app"
    }

    stages {

        stage('Clone repo') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/NourJbeli12/ProjetStudentsManagement-DevOps.git'
            }
        }

        stage('Build Maven') {
            steps {
                sh "mvn clean package -DskipTests"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:latest ."
            }
        }

        stage('Login to DockerHub') {
            steps {
                sh "echo ${DOCKERHUB_PSW} | docker login -u ${DOCKERHUB_USR} --password-stdin"
            }
        }

        stage('Push Docker Image') {
            steps {
                sh "docker push ${IMAGE_NAME}:latest"
            }
        }

        stage('Run container on Vagrant VM') {
            steps {
                sh """
                docker rm -f student-container || true
                docker run -d --name student-container -p 8080:8080 ${IMAGE_NAME}:latest
                """
            }
        }
    }
}

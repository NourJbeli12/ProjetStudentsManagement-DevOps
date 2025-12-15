pipeline {
    agent any

    tools {
        maven 'Maven-3'
        jdk 'JDK17'
    }

    environment {
        IMAGE_NAME = "nourjbelil23/student-app"
        K8S_NAMESPACE = "devops"
    }

    stages {

        stage('Git Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/NourJbeli12/ProjetStudentsManagement-DevOps.git'
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withCredentials([string(credentialsId: 'jenkins-sonar', variable: 'SONAR_TOKEN')]) {
                    withSonarQubeEnv('SonarQube') {
                        sh '''
                        mvn sonar:sonar \
                        -Dsonar.projectKey=students-management \
                        -Dsonar.projectName="Students Management" \
                        -Dsonar.login=$SONAR_TOKEN
                        '''
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:latest ."
            }
        }

        stage('Push Docker Image to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-hub-credentials',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                        docker push ${IMAGE_NAME}:latest
                    '''
                }
            }
        }

        stage('Deploy to Minikube (Kubernetes)') {
            steps {
                sh '''
                kubectl apply -f k8s/namespace.yaml || true
                kubectl apply -f k8s/deployment.yaml
                kubectl apply -f k8s/service.yaml
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline terminé avec succès (Docker + Minikube + SonarQube)'
        }
        failure {
            echo '❌ Pipeline échoué'
        }
    }
}

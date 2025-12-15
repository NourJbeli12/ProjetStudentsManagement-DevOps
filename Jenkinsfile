pipeline {
    agent any

    tools {
        maven 'Maven-3'
        jdk 'JDK17'
    }

    environment {
        SONAR_PROJECT_KEY = 'students-management'
        SONAR_PROJECT_NAME = 'Students Management'
    }

    stages {

        stage('Checkout GitHub') {
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
                withSonarQubeEnv('SonarQube') {
                    sh """
                    mvn sonar:sonar \
                      -Dsonar.projectKey=${SONAR_PROJECT_KEY} \
                      -Dsonar.projectName="${SONAR_PROJECT_NAME}"
                    """
                }
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline terminé avec succès'
        }
        failure {
            echo '❌ Pipeline échoué'
        }
    }
}


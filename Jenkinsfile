pipeline {
    agent any

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
    }
}

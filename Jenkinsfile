pipeline {
    agent any

    /* =========================
       TOOLS (OBLIGATOIRE)
       ========================= */
    tools {
        maven 'Maven-3'   // Nom EXACT configuré dans Jenkins > Tools
    }

    /* =========================
       VARIABLES D’ENVIRONNEMENT
       ========================= */
    environment {
        DOCKER_IMAGE = "nourjbeli/student-management:latest"
        SONAR_HOST_URL = "http://192.168.50.4:9000"
    }

    stages {

        /* =========================
           1️⃣ CLONE GITHUB
           ========================= */
        stage('Checkout GitHub') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/NourJbeli12/ProjetStudentsManagement-DevOps.git'
            }
        }

        /* =========================
           2️⃣ BUILD MAVEN
           ========================= */
        stage('Build with Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        /* =========================
           3️⃣ TESTS
           ========================= */
        stage('Run Tests') {
            steps {
                sh 'mvn test'
            }
        }

        /* =========================
           4️⃣ ANALYSE SONARQUBE
           ========================= */
        stage('SonarQube Analysis') {
            steps {
                withCredentials([string(credentialsId: 'jenkins-sonar', variable: 'SONAR_TOKEN')]) {
                    sh """
                        mvn sonar:sonar \
                        -Dsonar.host.url=${SONAR_HOST_URL} \
                        -Dsonar.login=${SONAR_TOKEN}
                    """
                }
            }
        }

        /* =========================
           5️⃣ BUILD IMAGE DOCKER
           ========================= */
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        /* =========================
           6️⃣ PUSH DOCKER HUB
           ========================= */
        stage('Push Docker Image') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker-hub-credentials',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh """
                        echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin
                        docker push $DOCKER_IMAGE
                    """
                }
            }
        }
    }

    /* =========================
       POST ACTIONS
       ========================= */
    post {
        success {
            echo "✅ Pipeline réussi"
        }
        failure {
            echo "❌ Pipeline échoué"
        }
    }
}

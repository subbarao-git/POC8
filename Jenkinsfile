pipeline {
    agent any

    environment {
        IMAGE_NAME = "subbu2712/java-app-sonar"
        SONARQUBE = "SonarQube"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn package -DskipTests'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${SONARQUBE}") {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Debug') {
            steps {
                sh 'ls -la'
                sh 'find . -name report-task.txt'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                    )]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh 'docker push $IMAGE_NAME:latest'
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker run -d -p 8081:8080 --name sonar-app $IMAGE_NAME:latest'
            }
        }
    }
}

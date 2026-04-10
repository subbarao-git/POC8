pipeline {
    agent any

    environment {
        IMAGE_NAME = "subbu2712/java-app-sonar"
        SONARQUBE = "SonarQube-Server"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch:'main', url:'https://github.com/subbarao-git/POC8.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package -DskipTests'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${SONARQUBE}") {
                    sh 'mvn sonar:sonar'
                }
            }
        }

                stage('Quality Gate') {
            steps {
                waitForQualityGate abortPipeline: true
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
                sh 'docker run -d -p 8081:8080 --name sonar $IMAGE_NAME:latest'
            }
        }
    }
}

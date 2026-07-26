pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t docker-test-app .'
            }
        }

        stage('Run Docker Container') {
            steps {
                sh '''
                docker stop docker-test-container || true
                docker rm docker-test-container || true
                docker run -d \
                --name docker-test-container \
                -p 8081:80 \
                docker-test-app
                '''
            }
        }
    }
}
pipeline {

    agent any

    environment {
    IMAGE_NAME = 'docker-test-app'
    DOCKERHUB_USERNAME = 'odocks'
}

    stages {

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .'
            }
        }

        
        stage('Push Docker Image') {
    steps {
        withCredentials([
            usernamePassword(
                credentialsId: 'dockerhub',
                usernameVariable: 'DOCKER_USER',
                passwordVariable: 'DOCKER_PASS'
            )
        ]) {

            sh '''
            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin

            docker tag ${IMAGE_NAME}:${BUILD_NUMBER} ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${BUILD_NUMBER}

            docker push ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:${BUILD_NUMBER}
            '''
        }
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
                ${IMAGE_NAME}:${BUILD_NUMBER}
                '''
            }
        }
    }
}
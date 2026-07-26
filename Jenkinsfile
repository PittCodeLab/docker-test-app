pipeline {

    agent any

    environment {
        IMAGE_NAME = 'docker-test-app'
        DOCKERHUB_USERNAME = 'odocks'
        IMAGE_TAG = "${BUILD_NUMBER}"
        DOCKER_IMAGE = "${DOCKERHUB_USERNAME}/${IMAGE_NAME}"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .'
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

                    docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${DOCKER_IMAGE}:${IMAGE_TAG}

                    docker push ${DOCKER_IMAGE}:${IMAGE_TAG}
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
                ${IMAGE_NAME}:${IMAGE_TAG}
                '''
            }
        }

        stage('Docker Cleanup') {
            steps {
                sh '''
                docker image prune -f

                docker images ${IMAGE_NAME} \
                --format "{{.ID}}" \
                | tail -n +6 \
                | xargs -r docker rmi
                '''
            }
        }
    }

    post {

        success {
            echo "Pipeline completed successfully"
        }

        failure {
            echo "Pipeline failed"
        }

        always {
            echo "Pipeline finished"
        }
    }
}
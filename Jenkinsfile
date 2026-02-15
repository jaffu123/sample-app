pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "jaffu123/sample-app"
        DOCKER_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Code checkout complete"
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} ."
            }
        }

        stage('Run Container') {
            steps {
                sh """
                docker stop app || true
                docker rm app || true
                docker run -d --name app -p 8081:80 ${DOCKER_IMAGE}:${DOCKER_TAG}
                """
            }
        }
    }
}

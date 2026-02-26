pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "jaffu23/myapp"
    }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/jaffu123/sample-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Login to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                sh 'docker push $DOCKER_IMAGE'
            }
        }

        stage('Deploy to Docker Server') {
            steps {
                sshagent(['docker-server-ssh']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@43.205.120.214 "
                    docker pull jaffu23/myapp &&
                    docker stop myapp || true &&
                    docker rm myapp || true &&
                    docker run -d -p 8080:80 --name myapp jaffu23/myapp
                    "
                    '''
                }
            }
        }
    }
}

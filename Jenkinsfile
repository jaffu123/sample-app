pipeline {
    agent any

    environment {
        IMAGE_NAME = "jaffu23/myapp"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Login to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                sh 'docker push $IMAGE_NAME'
            }
        }

        stage('Deploy to Docker Server') {
            steps {
                sshagent(['docker-server-ssh']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@172.31.33.171 << EOF
                    docker pull jaffu23/myapp
                    docker stop myapp || true
                    docker rm myapp || true
                    docker run -d -p 8080:8080 --name myapp jaffu23/myapp
                    EOF
                    '''
                }
            }
        }
    }
}

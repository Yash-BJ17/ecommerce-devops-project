pipeline {
    agent any

    environment {
        DOCKERHUB_USER = "yash0717"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Product Image') {
            steps {
                sh 'docker build -t $DOCKERHUB_USER/product-service:latest product-service/'
            }
        }

        stage('Build Order Image') {
            steps {
                sh 'docker build -t $DOCKERHUB_USER/order-service:latest order-service/'
            }
        }

        stage('Build Frontend Image') {
            steps {
                sh 'docker build -t $DOCKERHUB_USER/frontend:latest frontend/'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    '''
                }
            }
        }

        stage('Push Images') {
            steps {
                sh '''
                docker push $DOCKERHUB_USER/product-service:latest
                docker push $DOCKERHUB_USER/order-service:latest
                docker push $DOCKERHUB_USER/frontend:latest
                '''
            }
        }

        stage('Deploy To Kubernetes') {
            steps {
                sh 'kubectl apply -f k8s/'
            }
        }
    }
}

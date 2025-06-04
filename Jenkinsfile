pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = 'docker-hub-creds'     
        DOCKERHUB_USER = 'ahmedgamal01'
        IMAGE_NAME = 'ahmedgamal01/flask-app'
        IMAGE_TAG = "build-${BUILD_NUMBER}"
        FULL_IMAGE = "${IMAGE_NAME}:${IMAGE_TAG}"
    }

    stages {
        stage('Clone Repo') {
            steps {
                git url: 'https://github.com/eeahmedgamal/flask-kubernetes.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $FULL_IMAGE .'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: "$DOCKERHUB_CREDENTIALS", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push $FULL_IMAGE'
            }
        }

        stage('Deploy to K3s') {
            steps {
                sh '''
                sed "s|{{IMAGE}}|$FULL_IMAGE|g" k8s/deployment.yaml | kubectl apply -f -
                kubectl apply -f k8s/service.yaml
                '''
            }
        }
    }

    post {
        always {
            sh 'docker logout'
        }
    }
}

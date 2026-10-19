pipeline {
    agent any

    environment {
        IMAGE_NAME = 'usernamedockerhub/simple-app'
        REGISTRY_CREDENTIALS = 'dockerhub-credentials'
    }

    stages {
        stage('Checkout') { steps { checkout scm } }

        stage('Build Docker Image') {
            steps { sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER .' }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: REGISTRY_CREDENTIALS,
                        usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh 'docker push $IMAGE_NAME:$BUILD_NUMBER'
                    sh 'docker tag $IMAGE_NAME:$BUILD_NUMBER $IMAGE_NAME:latest'
                    sh 'docker push $IMAGE_NAME:latest'
                }
            }
        }

        stage('Deploy (App + Database)') {
            steps { sh 'docker compose up -d' }
        }
    }

    post { always { echo 'Selesai build' } }
}

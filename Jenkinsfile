pipeline {
    agent any
    tools {
        jdk 'jdk17'
        maven 'maven'
    }
    environment {
        AWS_DEFAULT_REGION = 'ap-south-1'
        ECR_REPOSITORY = '591334581876.dkr.ecr.ap-south-1.amazonaws.com/mana'
        APP_NAME = 'my-image'
        DOCKER_USER = 'aws'
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'ec', credentialsId: 'github', url: 'https://github.com/majjinarayanarao/register-app.git'
            }
        }
        stage('Build') {
            steps {
                script {
                    sh 'docker build -t ${APP_NAME} .'
                }
            }
        }
        stage('Push to ECR') {
            steps {
                script {
                    withAWS(region: AWS_DEFAULT_REGION, credentials: 'aws') {
                        sh "aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${ECR_REPOSITORY}"
                        sh "docker tag ${APP_NAME} ${ECR_REPOSITORY}:latest"
                        sh "docker push ${ECR_REPOSITORY}:latest"
                    }
                }
            }
        }
        stage('Cleanup Artifacts') {
            steps {
                script {
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker rmi ${APP_NAME}:latest"
                }
            }
        }
    }
}

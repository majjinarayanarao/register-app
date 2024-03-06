pipeline {
    agent any

    environment {
        DOCKER_IMAGE_NAME = 'my-docker-image'
        ECR_REGISTRY = '591334581876.dkr.ecr.ap-south-1.amazonaws.com/mana'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/your/repository.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE_NAME}:${env.BUILD_ID}")
                }
            }
        }

        stage('Push to ECR') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'awscred', variable: 'jenkins')]) {
                        docker.withRegistry("${ECR_REGISTRY}", 'ecr') {
                            docker.image("${DOCKER_IMAGE_NAME}:${env.BUILD_ID}").push()
                        }
                    }
                }
            }
        }
    }
}

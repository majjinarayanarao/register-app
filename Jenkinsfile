pipeline {
    agent any
    tools {
        jdk 'jdk17'
        maven 'maven'
    }
    
    environment {
        // Define your environment variables here
        DOCKER_REGISTRY_CREDENTIALS = 'docker'
        DOCKER_IMAGE = 'iov'
        DOCKER_USER = '591334581876.dkr.ecr.ap-south-1.amazonaws.com/mana'
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
                    // Build Maven project
                    sh 'mvn clean package'
                }
            }
        }

        stage("Build & Push Docker Image") {
            steps {
                script {
                    docker.withRegistry('', DOCKER_REGISTRY_CREDENTIALS) {
                        def dockerImage = docker.build("${DOCKER_IMAGE}")
                        dockerImage.push("${DOCKER_USER}:${IMAGE_TAG}")
                        dockerImage.push("${DOCKER_USER}:latest")
                    }
                }
            }
        }
    }
}

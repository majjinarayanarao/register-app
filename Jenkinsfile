pipeline {
    agent any
    
    environment {
        RELEASE = "1.0.0"
        DOCKER_USER = "mnr143"
        DOCKER_IMAGE_NAME = 'l'
        DOCKER_IMAGE_TAG = 'latest'
        DOCKER_REGISTRY_CREDENTIALS = "manasa"
    }
    
    tools {
        jdk 'jdk17'
        maven 'maven'
        // Add Docker tool
        dockerTool 'mm'
    }
    
    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
            steps {
                git branch: 'masa', url: 'https://github.com/majjinarayanarao/register-app.git'
            }
        }

        stage("Build Application") {
            steps {
                sh "mvn clean package"
            }
        }

        stage("Test Application") {
            steps {
                sh "mvn test"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Authenticate and push the Docker image
                    docker.withRegistry('https://hub.docker.com', DOCKER_REGISTRY_CREDENTIALS) {
                     sh "docker push ${DOCKER_USER}/${DOCKER_IMAGE_NAME}:latest"
                    }
                }
            }
        }
    }
}

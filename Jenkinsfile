pipeline {
    agent any
    tools {
        jdk 'jdk17'
        maven 'maven'
        // Add Docker tool
        dockerTool 'docker'
    }
    environment {
        RELEASE = "1.0.0"
        DOCKER_USER = "mnr143"
        DOCKER_IMAGE_NAME = "maaa"
        DOCKER_IMAGE_TAG = "latest"
        DOCKER_REGISTRY_CREDENTIALS = "i"
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
                    docker.withRegistry('https://hub.docker.com', DOCKER_REGISTRY_CREDENTIALS) {
                        docker.image("${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}").push()
                    }
                }
            }
        }

        stage('Image Scan') {
            steps {
                script {
                    // Perform image scan using Trivy
                    sh "docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy:latest image -f table --no-progress --severity HIGH,CRITICAL --exit-code 0 ${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG} > trivyimage.txt"
                }
            }
        }
    }
}

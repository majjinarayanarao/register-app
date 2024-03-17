pipeline {
    agent any
    tools {
        jdk 'jdk17'
        maven 'maven'
        // Add SonarQube Scanner tool
        tool 'SonarQube-Scanner'
        // Add Docker tool
        dockerTool 'docker'
    }
    environment {
        APP_NAME = "maaa"
        RELEASE = "1.0.0"
        DOCKER_USER = "mnr143"
        DOCKER_PASS = credentials('i')
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    }
    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
            steps {
                git branch: 'real', url: 'https://github.com/majjinarayanarao/register-app.git'
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

        stage('OWASP FS SCAN') {
            steps {
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'default'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }

        stage('TRIVY FS SCAN') {
            steps {
                script {
                    sh 'trivy fs . > trivyfs.txt'
                }
            }
        }

        stage("Build & Push Docker Image") {
            steps {
                script {
                    def dockerImage
                    docker.withRegistry('https://hub.docker.com/', DOCKER_USER, DOCKER_PASS) {
                        dockerImage = docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                        dockerImage.push('latest') 
                    }
                }
            }
        }

        stage('Image Scan') {
            steps {
                script {
                    sh 'docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy:latest image -f table --no-progress --severity HIGH,CRITICAL --exit-code 0 ${IMAGE_NAME}:${IMAGE_TAG} > trivyimage.txt'
                }
            }
        }
    }
}

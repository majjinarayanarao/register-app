pipeline {
    agent any
    tools {
        jdk 'jdk17'
        maven 'maven'
    }
    environment {
        APP_NAME = "maaa"
        RELEASE = "1.0.0"
        DOCKER_USER = "mnr143"
        DOCKER_PASS = 'dockerhub'
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
        SCANNER_HOME = tool 'SonarQube-Scanner'
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
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'dk'
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
         stage('Build') {
            steps {
                script {
                    sh 'docker build -t ${APP_NAME} .'
                }
            }
        }
        stage('image scan') {
            steps {
                script {
                     sh ('docker run -v /var/run/docker.sock:/var/run/docker.sock trivy image mnr143/maaa:latest --no-progress --scanners vuln  --exit-code 0 --severity HIGH,CRITICAL --format table >  trivyimage.txt')
                }
            }
        }
    }
}

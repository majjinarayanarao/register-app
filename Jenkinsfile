pipeline {
    agent any
    tools {
        jdk 'jdk17'
        maven 'maven'
    }
    environment {
        SCANNER_HOME = tool 'SonarQube-Scanner'
        APP_NAME = "register-app-pipeline"
        RELEASE = "1.0.0"
        DOCKER_USER = "mnr143"
        DOCKER_PASS = 'docker'
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
                git branch: 'new', credentialsId: 'github', url: 'https://github.com/majjinarayanarao/register-app.git'
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

        stage('CODE ANALYSIS with SONARQUBE') {
            environment {
                scannerHome = tool 'SonarQube-Scanner'
            }
            steps {
                withSonarQubeEnv('SonarQube-installations') {
                    sh """${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=mnr143 \
                        -Dsonar.host.url=http://52.66.111.240:9000 \
                        -Dsonar.login=63209d914e4bd28b96fca3e932fcb81f95406718 \
                        -Dsonar.projectVersion=1.0 \
                        -Dsonar.sources=src/ \
                        -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                        -Dsonar.junit.reportsPath=target/surefire-reports/ \
                        -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                        -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml"""
                }
                timeout(time: 10, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage("Build & Push Docker Image") {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_USER, DOCKER_PASS) {
                        def dockerImage = docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                        dockerImage.push()
                        dockerImage.push('latest')
                    }
                }
            }
        }

        stage("Trivy Scan") {
            steps {
                script {
                    sh "docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image ${IMAGE_NAME}:latest --no-progress --scanners vuln --exit-code 0 --severity HIGH,CRITICAL --format table"
                }
            }
        }

        stage('Cleanup Artifacts') {
            steps {
                script {
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker rmi ${IMAGE_NAME}:latest"
                }
            }
        }
    }
}

pipeline {
    agent any
    tools {
        jdk 'jdk17'
        maven 'maven'
    }
    environment {
        AWS_DEFAULT_REGION = 'ap-south-1'
        ECR_REPOSITORY = '59.dkr.ecr.ap-south-1.amazonaws.com/maa'
        APP_NAME = 'my-image'
        DOCKER_USER = 'maa'
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
    }
}

        stage('SonarQube Analysis') {
            steps {
                script {
                    // Perform SonarQube analysis
                    withSonarQubeEnv('SonarQube-installations') {
                        sh '''
                            mvn clean verify sonar:sonar \
                              -Dsonar.projectKey=youtube \
                              -Dsonar.host.url=http://3.110.178.54:9000 \
                              -Dsonar.login=squ_eddd02a7725bb811c9ca39fac4d86e9c9d25b5d7
                        '''
                    }
                }
            }
        }
        
        stage('Quality Gate') {
            steps {
                script {
                    // Wait for quality gate
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonar'
                }
            }
        }
    }
}

aws cil must be install in jenkins
jenkins cred lo aws add cheyali
aws piplines setp aws plugin
docker plugin inttsllation mustbe done .

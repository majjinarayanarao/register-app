pipeline {
    agent any
    tools {
        jdk 'jdk17'
        maven 'maven'
    }
    environment {
        AWS_DEFAULT_REGION = 'ap-south-1'
        ECR_REPOSITORY = '591334581876.dkr.ecr.ap-south-1.amazonaws.com/mana'
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'ec', credentialsId: 'github', url: 'https://github.com/majjinarayanarao/register-app.git'
            }
        }
        stage('Build') {
            steps {
                sh 'docker build -t my-image .'
            }
        }
        stage('Push to ECR') {
            steps {
                withAWS(region: AWS_DEFAULT_REGION, credentials: 'aws') {
                    sh "aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${ECR_REPOSITORY}"
                    sh "docker tag my-image ${ECR_REPOSITORY}:latest"
                    sh "docker push ${ECR_REPOSITORY}:latest"
                }
            }
        }
    }
}
aws cil must be install in jenkins
jenkins cred lo aws add cheyali
aws piplines setp aws plugin
docker plugin inttsllation mustbe done .

pipeline {
    agent any
    tools {
        jdk 'jdk17'
        maven 'maven'
    }
    environment {
        SCANNER_HOME = tool 'SonarQube_Scanner'
    }
    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/majjinarayanarao/register-app.git'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                script {
                    sh '''
                        mvn clean verify sonar:sonar \
                          -Dsonar.projectKey=youtube \
                          -Dsonar.host.url=http://35.154.240.187:9000 \
                          -Dsonar.login=squ_eddd02a7725bb811c9ca39fac4d86e9c9d25b5d7
                    '''
                }
            }
        }
        
        stage('Quality Gate') {
            steps {
                script {
                     waitForQualityGate abortPipeline: false, credentialsId: 'sonar' 
                    }
                }
            }
    }
}

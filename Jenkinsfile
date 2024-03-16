pipeline {
    agent any
    tools {
        jdk 'jdk17'
        maven 'maven'
    }
    environment {
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
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/majjinarayanarao/register-app.git'
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                script {
                    // Perform SonarQube analysis
                    withSonarQubeEnv('SonarQube') {
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

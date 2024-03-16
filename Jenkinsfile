pipeline {
    agent any
    tools {
        jdk 'jdk17'
        maven 'maven'
        Dependency-Check 'dk'
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
                dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'Dk'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage('TRIVY FS SCAN') {
            steps {
                script {
                    sh 'trivy fs > trivyfs.txt'
                }
            }
        }
    }
}

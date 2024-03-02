pipeline {
    agent any
    tools {
        jdk 'jdk17'
        maven 'maven'
    }
    environment {
        registry = "mnr143/rk1"
        registryCredential = 'docker'
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

        stage('Building image') {
            steps {
                script {
                    docker.build(registry + ":$BUILD_NUMBER")
                }
            }
        }
        
        stage('Push Docker') {
            steps {
                echo 'Push Docker'
                script {
                    def dockerImage = docker.image(registry + ":$BUILD_NUMBER")
                    dockerImage.push()
                    dockerImage.push('latest')
                }
            }
        }
    }
}

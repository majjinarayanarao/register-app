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
            IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
            IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    }
    stages{
        stage("Cleanup Workspace"){
                steps {
                cleanWs()
                }
        }

        stage("Checkout from SCM"){
                steps {
                    git branch: 'new', credentialsId: 'github', url: 'https://github.com/majjinarayanarao/register-app.git'
                }
        }

        stage("Build Application"){
            steps {
                sh "mvn clean package"
            }

       }

       stage("Test Application"){
           steps {
                 sh "mvn test"
           }
       }
        stage("Build & Push Docker Image") {
            steps {
                script {
                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image = docker.build "${IMAGE_NAME}"
                    }

                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }
                }
            }
	stage("Trivy Scan") {
           steps {
               script {
	            sh ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image mnr143/register-app-pipeline:latest --no-progress --scanners vuln  --exit-code 0 --severity HIGH,CRITICAL --format table')
               }
           }
       }
	 stage ('Cleanup Artifacts') {
           steps {
               script {
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker rmi ${IMAGE_NAME}:latest"
               }
          }
       }
   }
}
def COLOR_MAP = [
    'FAILURE' : 'danger',
    'SUCCESS' : 'good'
]

pipeline {
    agent any

    stages {
        stage('Example Stage') {
            steps {
                echo 'Hello' // This is where you define your stage steps
            }
        }
    }

    post {
        always {
            echo 'Slack Notifications'
            slackSend (
                channel: '#jenkins', // Adjust the channel name as needed
                color: COLOR_MAP[currentBuild.currentResult],
                message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} \n build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}",
                notifyCommitters: true, // Corrected parameter name
                tokenCredentialId: 'slack'
            )
        }
    }
}


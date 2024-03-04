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
            // Login to JFrog Artifactory
            def server = Artifactory.server 'Artifactory_Server_Name'
            server.credentialsId = 'Artifactory_Credentials_Id'

            // Build Docker image
            def dockerImage = docker.build "${IMAGE_NAME}"

            // Tag the image
            docker.image("${IMAGE_NAME}").tag("${IMAGE_TAG}")

            // Push the image to JFrog Artifactory
            docker.withRegistry("https://your-artifactory-url.com", "Artifactory_Credentials_Id") {
                dockerImage.push("${IMAGE_TAG}")
                dockerImage.push('latest')
            }
        }
    }
}
Artifactory_Server_Name: Name of the JFrog Artifactory server configured in Jenkins.
Artifactory_Credentials_Id: ID of the credentials configured in Jenkins to authenticate with JFrog Artifactory.
https://your-artifactory-url.com: URL of your JFrog Artifactory instance.
IMAGE_NAME: Name of your Docker image.
IMAGE_TAG: Tag of your Docker image.
This modified stage will build your Docker image, tag it appropriately, and then push it to your specified JFrog Artifactory repository. Make sure you have the necessary permissions and configurations set up in your JFrog Artifactory instance for Docker image management.






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

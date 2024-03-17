pipeline {
    agent any
    tools {
        jdk 'jdk17'
        maven 'maven'
    }
    environment {
	    APP_NAME = "regi"
            RELEASE = "1.0.0"
            DOCKER_USER = "mnr143"
            DOCKER_PASS = 'manasa'
            IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
            IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
	    SCANNER_HOME = tool 'SonarQube-Scanner'
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
       }
	stage("Trivy Scan") {
           steps {
               script {
	            sh 'trivy image mnr143/regi:latest > trivyimage.txt'
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

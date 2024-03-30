pipeline {
    agent any
    tools {
        jdk 'jdk17'
        maven 'maven'
        // Add Docker tool
        dockerTool 'docker'
    }
    environment {
        APP_NAME = "maaa"
        RELEASE = "1.0.0"
        DOCKER_USER = "mnr143"
        DOCKER_PASS = credentials('i')
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
                    def dockerImage
                    docker.withRegistry('https://hub.docker.com/', DOCKER_USER, DOCKER_PASS) {
                        dockerImage = docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                        dockerImage.push('latest') 
                    }
                }
            }
        }

        stage('Image Scan') {
            steps {
                script {
                    sh 'docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy:latest image -f table --no-progress --severity HIGH,CRITICAL --exit-code 0 ${IMAGE_NAME}:${IMAGE_TAG} > trivyimage.txt'
                }
            }
        }
    }
}
final ending :
pipeline {
    agent any
    
    environment {
        GIT_REPO_NAME = "git1"
        GIT_USER_NAME = "majjinarayanarao"
        GITHUB_TOKEN = ""
    } 
    stages {
        stage('Update Deployment File') {
             steps {
                 withCredentials([string(credentialsId: 'github', variable: 'GITHUB_TOKEN')]) {
                 sh """
                        git init
                        git checkout  main
                        git branch
                        echo 'Some content' >> naru
                        git config user.email 'abhishek.xyz@gmail.com'
                        git config user.name 'Abhishek Veeramalla'
                        git branch
                        git add naru
                        git commit -m 'Update deployment file'
                        git config pull.rebase false
                        git config pull.rebase true
                        git config pull.ff only
                        git fetch origin main:tmp
                        git rebase tmp
                        git push --set-upstream origin main
                    """
                }
            }
        }
    }
}

pipeline {
    agent any

    tools {
        jdk 'Java17'        // Make sure Java17 is configured in Jenkins Global Tool Configuration
        maven 'Maven3'      // Make sure Maven3 is configured too
    }
      environment {
        APP_NAME = "Devops-Mega-project"
        RELEASE = "1.0.0"
        DOCKER_USER = "rahul699"
        DOCKER_PASS = 'dockerhub'
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
        // JENKINS_API_TOKEN = credentials("JENKINS_API_TOKEN")

    }
    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
            steps {
                git branch: 'main',
                    credentialsId: 'github',
                    url: 'https://github.com/Rahulu56/Devops-Mega-project.git'
            }
        }

        stage("Build the Application") {
            steps {
                sh "mvn clean package"
            }
        }

        stage("Test the Application") {
            steps {
                sh "mvn test"
            }
        }
        stage("Sonarqube Analysis") {
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token'){
                        sh "mvn sonar:sonar"
                    }
                }
            }
        }

        stage("Quality Gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-token'
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
    }
}
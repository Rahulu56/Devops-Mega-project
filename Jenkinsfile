pipeline {
    agent any

    tools {
        jdk 'Java17'        // Make sure Java17 is configured in Jenkins Global Tool Configuration
        maven 'Maven3'      // Make sure Maven3 is configured too
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
                sh "mvn clean package -DskipTests"
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
    }
}
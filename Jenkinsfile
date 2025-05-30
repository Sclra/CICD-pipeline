pipeline {
    agent any

    environment {
        IMAGE_NAME = 'myusername/my-app' // your Docker repo/name
    }

    stages {

        stage("Git Clone") {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: 'master']],
                    extensions: [
                        [$class: 'RelativeTargetDirectory', relativeTargetDir: 'app']
                    ],
                    userRemoteConfigs: [[url: 'https://github.com/Sclra/java-hello-world-with-maven.git']]
                ])
            }
        }

        stage("Build JAR") {
            steps {
                echo "============================== Build JAR =============================="
                sh "cd app && mvn clean package"
            }
        }

        stage("Test with SonarQube") {
            steps {
                echo "======== Executing SonarQube Analysis ========"
                withSonarQubeEnv(credentialsId: 'sonarqube-server', installationName: 'sonarqube-server') {
                    sh '''
                        cd app && mvn clean verify sonar:sonar \
                        -Dsonar.projectKey=test \
                        -Dsonar.projectName=test
                    '''
                }
            }
        }

        stage("Quality Gate") {
            steps {
                timeout(time: 15, unit: 'MINUTES') {
                waitForQualityGate abortPipeline: true
                }
            }
        }

        stage("Build Docker Image") {
            steps {
                script {
                    def imageTag = "${env.IMAGE_NAME}:build-${env.BUILD_NUMBER}"
                    def customImage = docker.build(imageTag)
                    echo "Built Docker image: ${imageTag}"
                }
            }
        }
    }
}

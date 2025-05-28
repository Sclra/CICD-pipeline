pipeline {
    agent any
    environment {
        IMAGE_NAME = 'myusername/my-app'   // your Docker repo/name
    }
    stages {
        stage("git clone") {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: 'master']],
                    extensions: [
                        [$class: 'RelativeTargetDirectory', relativeTargetDir: 'app']   // put folder name as string
                    ],
                    userRemoteConfigs: [[url: 'https://github.com/Sclra/java-hello-world-with-maven.git']]
                ])
            }
        }
        stage("build jar"){
            steps{
                echo "==============================build jar=============================="
                sh "cd app && mvn clean package"
            }
        }
        stage("build dockerfile"){
            steps {
                script {
                    // Compose tag with Jenkins build number
                    def imageTag = "${env.IMAGE_NAME}:build-${env.BUILD_NUMBER}"

                    // Build the docker image with dynamic tag
                    def customImage = docker.build(imageTag)
                    echo "Built Docker image: ${imageTag}"
                }
            }
        }
    }
}

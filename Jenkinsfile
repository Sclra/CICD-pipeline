pipeline {
    agent any

    environment {
        IMAGE_NAME = '192.168.56.23:6000/my-app' // Docker repo/name with registry
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
    stages {
        stage("Build JAR File") {
            steps {
                withMaven(
                    mavenSettingsConfig: 'nexus-maven'
                ) {
                sh "cd app && mvn clean deploy"
                }
            }
        }
    }
        stage("Build Docker Image") {
            steps {
                script {
                    env.IMAGE_TAG = "${IMAGE_NAME}:build-${BUILD_NUMBER}"
                    // Save to a global variable so we can use it later
                    dockerImage = docker.build(env.IMAGE_TAG)
                    echo "Built Docker image: ${env.IMAGE_TAG}"
                }
            }
        }

        stage("Push Docker Image") {
            steps {
                script {
                    docker.withRegistry("http://192.168.56.23:6000", 'nexus-repo') {
                        dockerImage.push()
                        echo "Pushed Docker image: ${env.IMAGE_TAG}"
                    }
                }
            }
        }
    }
}

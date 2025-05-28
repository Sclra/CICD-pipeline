pipeline{
    agent any
    stages{
        stage("build jar file"){
            steps {
                checkout([
                    $class: 'GitSCM', 
                    branches: [[name: 'master']],
                    extensions: [
                        [$class: 'RelativeTargetDirectory', relativeTargetDir: app]
                    ]
                    userRemoteConfigs: [[url: 'https://github.com/Sclra/java-hello-world-with-maven.git']]  
                ])
            }
        }
    }
}
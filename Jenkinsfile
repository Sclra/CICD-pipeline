pipeline{
    agent any
    stages{
        stage("build jar file"){
            steps {
                git branch: 'master',
                    credentialsId: 'Sclra',
                    url: 'https://github.com/Sclra/java-hello-world-with-maven.git'

                sh "ls -lat"
            }
        }
    }
}
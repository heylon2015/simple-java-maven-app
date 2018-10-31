pipeline {
    agent {
        docker {
            image 'maven:3.3.9-jdk-8-alpine' 
            args '-v /root/.m2:/root/.m2' 
        }
    }
    stages {
        stage('Build') { 
            steps {
                sh 'mvn -version' 
            }
        }
    }
}

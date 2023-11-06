pipeline {
    agent {
        docker {image 'gradle'}
    }
    stages {
        stage('Scan')  {
            steps {
                withSonarQubeEnv(installationName: 'sonarqube') {
                    sh "./gradlew sonar"
                }
            }
        }
    }
}
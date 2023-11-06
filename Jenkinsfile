pipeline {
    agent {
        docker {
            image 'gradle'
            args  '--net="jenkins"'
        }
    }
    stages {
        stage('Scan')  {
            steps {
                withSonarQubeEnv(installationName: 'sonarqube') {
                    sh "gradle sonar"
                }
            }
        }
    }
}
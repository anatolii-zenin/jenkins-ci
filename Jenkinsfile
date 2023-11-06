pipeline {
    agent {
        docker {
            image 'gradle'
            args  '--net="jenkins_network"'
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
pipeline {
    agent {
        docker {
            image 'gradle'
            args  '--net="jenkins"'
        }
    }
    stages {
        stage('Build')  {
            steps {
                sh "gradle build"
            }
        }
        stage('Test')  {
            steps {
                sh "gradle test"
            }
        }
        stage('Scan')  {
            steps {
                withSonarQubeEnv(installationName: 'sonarqube') {
                    sh "gradle sonar"
                }
            }
        }
    }
}
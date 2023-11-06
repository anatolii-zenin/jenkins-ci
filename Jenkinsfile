pipeline {
    agent {
        docker {image 'gradle'}
    }
    stages {
        stage('SCM') {
            checkout scm
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
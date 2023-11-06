pipeline {
    agent {
        docker {image 'gradle'}
    }
    stage('SCM') {
        checkout scm
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
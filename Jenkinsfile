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
        stage('Test and generate code coverage report')  {
            steps {
                sh "gradle module-main:testCodeCoverageReport"
            }
        }
        stage('Scan')  {
            steps {
                withSonarQubeEnv(installationName: 'sonarqube') {
                    sh "gradle sonar"
                }
            }
        }
        stage('Sleep') {
            steps {
                echo 'Waiting 5 minutes for debugging purposes'
                sleep 300
            }
        }
    }
}
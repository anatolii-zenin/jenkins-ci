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
        stage('Package war file')  {
            steps {
                sh "gradle module-main:bootWar"
            }
        }
    }
    post {
        always {
            sh "echo $PWD"
            archiveArtifacts artifacts: "$buildDir/../module-main/build/libs/module-main.war", onlyIfSuccessful: true
        }
    }
}
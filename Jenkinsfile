pipeline {
    agent { label 'pipe_slave' }
    tools {
        maven 'maven'
    }
    stages {
        stage ("git scm") {
            steps {
                git 'https://github.com/suresh1298/Sample_Project'
            }
        }
        stage ("sonar") {
            environment {
                scannerHome = tool 'sonarscanner'
            }
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }
        stage ("quality check") {
            steps {
                timeout(time: 10, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        stage ('maven build') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage ('nexus') {
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'happy', classifier: '', file: 'target/simple-web-app.war', type: 'war']], credentialsId: '59cfd18f-873c-4852-be70-8b4e8b5850d1', groupId: 'org.mitre', nexusUrl: '13.233.90.88:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'suresh-release', version: '2.1'
            }
            
        }
        stage ('tomcat') {
            steps {
                sh "sudo cp target/*.war /opt/tomcat/webapps"
            }
        }
    }
    post {
        success {
            emailext (
                to: 'reddysuresh1298@gmail.com',
                subject: "JOB: ${env.JOB_NAME} - SUCCESS",
                body: "JOB SUCCESS - \"${env.JOB_NAME}\" Build No: ${env.BUILD_NUMBER}\n\nClick on the below link to view the logs:\n ${env.BUILD_URL}\n"
            )
        }
        failure {
            emailext (
                to: 'reddysuresh1298@gmail.com',
                subject: "JOB: ${env.JOB_NAME} - FAILURE",
                body: "JOB FAILURE - \"${env.JOB_NAME}\" Build No: ${env.BUILD_NUMBER}\n\nClick on the below link to view the logs:\n ${env.BUILD_URL}\n"
            )
        }
    }
}

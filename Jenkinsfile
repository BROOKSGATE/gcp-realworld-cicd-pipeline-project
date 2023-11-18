def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE': 'danger',
    'UNSTABLE': 'danger'
]
pipeline {
    agent any
  
    stages {
        stage('Validate Project') {
            steps {
                sh 'mvn validate'
            }
        }
        stage('Unit Test'){
            steps {
                sh 'mvn test'
            }
        }
        stage('Integration Test'){
            steps {
                sh 'mvn verify -DskipUnitTests'
            }
        }
        stage('App Packaging'){
            steps {
                sh 'mvn package'
            }
        }
        stage ('Checkstyle Code Analysis'){
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
        }
        stage('SonarQube Inspection') {
            steps {
                sh  """mvn clean verify sonar:sonar \
                        -Dsonar.projectKey=java-web-app-project1 \
                        -Dsonar.host.url=http://10.188.0.3:9000 \
                        -Dsonar.login=sqp_0a07ac0bb2c49e44dcf72aa08ccc067814f0f881"""
            }
        }
        stage("Upload Artifact To Nexus"){
            steps{
                 sh 'mvn deploy'
            }
            post {
                success {
                  echo 'Successfully Uploaded Artifact to Nexus Artifactory'
                }
            }
        }
    }
    post {
    always {
        echo 'Slack Notifications.'
        slackSend channel: '#alexander-jenkins-ci-pipeline', //update and provide your channel name
        color: COLOR_MAP[currentBuild.currentResult],
        message: "*${currentBuild.currentResult}:* Job Name '${env.JOB_NAME}' build ${env.BUILD_NUMBER} \n Build Timestamp: ${env.BUILD_TIMESTAMP} \n Project Workspace: ${env.WORKSPACE} \n More info at: ${env.BUILD_URL}"
    }
  }
}

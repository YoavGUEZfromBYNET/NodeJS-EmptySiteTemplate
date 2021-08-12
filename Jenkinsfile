pipeline {
  agent {
    node {
      label 'centos7-nodejs'
    }

  }
  stages {
    stage('Check Out Code') {
      steps {
        git(url: ' https://github.com/YoavGUEZfromBYNET/NodeJS-EmptySiteTemplate.git', branch: 'master', poll: true, changelog: true)
      }
    }

    stage('Build & Compile') {
      steps {
        sh 'npm install'
      }
    }

    stage('Test Code') {
      steps {
        sh '''node server.js &
sleep 5 &&
curl localhost:8080
if [[ "x$?" == "x0" ]]; 
then    echo good; 
else exit 1; 
fi'''
      }
    }

    stage('Package Code') {
      steps {
        sh 'tar -czvf node.tar.gz * '
      }
    }

    stage('Publish the Archive') {
      parallel {
        stage('Publish the Archive') {
          steps {
            archiveArtifacts 'node.tar.gz'
            cleanWs(cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenNotBuilt: true, cleanWhenSuccess: true, cleanWhenUnstable: true)
          }
        }

        stage('Slack Send') {
          steps {
            sleep 1
            slackSend channel: 'yg-channel-private', color: '#3EA652', message: "${env.JOB_NAME} #${env.BUILD_NUMBER} - Started By ${env.BUILD_USER} (${env.BUILD_URL})"
          }
        }

      }
    }

  }
}

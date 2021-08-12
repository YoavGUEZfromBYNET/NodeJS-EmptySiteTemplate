def notifySlack(String buildStatus = 'STARTED') {
    // Build status of null means success.
    buildStatus = buildStatus ?: 'SUCCESS'

    def color

    if (buildStatus == 'STARTED') {
        color = '#D4DADF'
    } else if (buildStatus == 'SUCCESS') {
        color = '#BDFFC3'
    } else if (buildStatus == 'UNSTABLE') {
        color = '#FFFE89'
    } else {
        color = '#FF9FA1'
    }

    def msg = "${buildStatus}: `${env.JOB_NAME}` #${env.BUILD_NUMBER}:\n${env.BUILD_URL}"

    slackSend(color: color, message: msg)
}

pipeline {
  agent {
    node {
      label 'centos7-nodejs'
    }

  }
  stages {
    stage('Chewock Out Code') {
      steps {
        cleanWs(cleanWhenAborted: true, cleanWhenFailure: true, cleanWhenNotBuilt: true, cleanWhenSuccess: true, cleanWhenUnstable: true)
        git(url: 'https://github.com/YoavGUEZfromBYNET/NodeJS-EmptySiteTemplate.git', branch: 'master', changelog: true, poll: true)
      }
    }

    stage('Build Code') {
      steps {
        sh 'npm install'
      }
    }

    stage('Test Code') {
      steps {
        sh '''node server.js &
sleep 5 && 
curl localhost:8080 && if [[ "x$?" == "x0" ]]; then    echo good; else exit 1; fi
'''
      }
    }

    stage('Kill TEST') {
      steps {
        sh 'sudo pkill node'
      }
    }

    stage('Package Code') {
      parallel {
        stage('Package Code') {
          steps {
            sh 'tar -czvf node.tar.gz *'
          }
        }

        stage('Slack') {
          steps {
            slackSend (channel: slack_channel, color: colorCode, message: "${env.JOB_NAME} #${env.BUILD_NUMBER} - " + buildStatus + " Started By ${env.BUILD_USER} (${env.BUILD_URL})")
          }
        }

      }
    }

  }
}

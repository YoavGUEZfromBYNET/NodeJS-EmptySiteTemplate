pipeline {
  agent {
    node {
      label 'centos7-nodejs'
    }

  }
  stages {
    stage('Check Out Code') {
      steps {
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
      steps {
        sh 'tar -czvf node.tar.gz '
      }
    }

  }
}
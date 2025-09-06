pipeline {
  agent any

  options { timestamps() }
  triggers { pollSCM('H/5 * * * *') }

  tools { nodejs 'Node 18' }

  environment {
    XDG_CONFIG_HOME = "${WORKSPACE}/.config"
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/IDFK999/8.2CDevSecOps.git'
      }
    }

    stage('Install Dependencies') {
      steps {
        sh '''
          mkdir -p "$XDG_CONFIG_HOME"
          npm ci --no-progress --loglevel=error
        '''
      }
    }

    stage('Run Tests') {
      steps {
        sh 'npm test || true'
      }
      post {
        always {
          retry(2) {
            emailext(
              to: 's221133429@gmail.com',
              from: 's221133429@gmail.com',
              subject: "[DevSecOps] Run Tests — ${currentBuild.currentResult} (${env.JOB_NAME} #${env.BUILD_NUMBER})",
              body: "test test — Run Tests finished.\nJob: ${env.JOB_NAME}\nBuild: #${env.BUILD_NUMBER}"
            )
          }
        }
      }
    }

    stage('Generate Coverage Report') {
      steps {
        sh 'echo "No coverage script in this project; skipping."'
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        sh '''
          npm audit || true
          npm audit > npm-audit.txt || true
        '''
      }
      post {
        always {
          retry(2) {
            emailext(
              to: 's221133429@gmail.com',
              from: 's221133429@gmail.com',
              subject: "[DevSecOps] NPM Audit — ${currentBuild.currentResult} (${env.JOB_NAME} #${env.BUILD_NUMBER})",
              body: "test test — NPM Audit finished. See attachment for details.",
              attachmentsPattern: 'npm-audit.txt'
            )
          }
        }
      }
    }
  }
}

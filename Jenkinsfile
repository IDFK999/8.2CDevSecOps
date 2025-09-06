pipeline {
  agent any

  options { timestamps() }
  // Poll every 5 minutes to satisfy the “auto trigger without webhook” requirement
  triggers { pollSCM('H/5 * * * *') }

  // Ensure Node 18 from Jenkins NodeJS plugin is on PATH
  tools { nodejs 'Node_18' }

  // Avoid config permission issues for tools like snyk
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
          // Keep it small first; add attachLog after you confirm email works
          retry(2) {
            emailext(
              to: 's221133429@gmail.com',
              from: 's221133429@gmail.com',
              subject: "[DevSecOps] Run Tests — ${currentBuild.currentResult} (${env.JOB_NAME} #${env.BUILD_NUMBER})",
              body: "test test — Run Tests finished.\nJob: ${env.JOB_NAME}\nBuild: #${env.BUILD_NUMBER}"
              // attachLog: true, compressLog: true
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
              // You can also add: attachLog: true, compressLog: true
            )
          }
        }
      }
    }
  }
}

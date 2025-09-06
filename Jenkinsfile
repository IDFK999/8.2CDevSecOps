pipeline {
  agent any

  tools { nodejs 'Node 18' }                  // Manage Jenkins → Tools → NodeJS installations

  triggers { pollSCM('H/5 * * * *') }         // auto-run every ~5 minutes on new commits

  environment {
    NOTIFY = 's221133429@gmail.com'
    XDG_CONFIG_HOME = "${env.WORKSPACE}/.config" // avoid snyk EACCES during npm test
    NODEJS_HOME = "${tool 'Node 18'}"
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
BIN_DIR=""
if [ -d "${NODEJS_HOME}/bin" ]; then BIN_DIR="${NODEJS_HOME}/bin"; else BIN_DIR=$(ls -d "${NODEJS_HOME}"/node-v*/bin 2>/dev/null | head -n1); fi
export PATH="$BIN_DIR:$PATH"
mkdir -p "${XDG_CONFIG_HOME}"
node -v && npm -v
npm ci --no-progress --loglevel=error || npm install --no-progress --loglevel=error
'''
      }
    }

    stage('Run Tests') {
      steps { sh 'npm test || true' }
      post {
        always {
          sleep(time: 5, unit: 'SECONDS')
          retry(3) {
            emailext(
              to: "${env.NOTIFY}",
              subject: "[SIT223 DevSecOps] ${env.STAGE_NAME} — ${currentBuild.currentResult} (${env.JOB_NAME} #${env.BUILD_NUMBER})",
              body: "test test — ${env.STAGE_NAME} notification.",
              attachLog: true, compressLog: true, mimeType: 'text/plain'
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
      steps { sh 'npm audit || true' }
      post {
        always {
          sleep(time: 5, unit: 'SECONDS')
          retry(3) {
            emailext(
              to: "${env.NOTIFY}",
              subject: "[SIT223 DevSecOps] ${env.STAGE_NAME} — ${currentBuild.currentResult} (${env.JOB_NAME} #${env.BUILD_NUMBER})",
              body: "test test — ${env.STAGE_NAME} notification.",
              attachLog: true, compressLog: true, mimeType: 'text/plain'
            )
          }
        }
      }
    }
  }
}

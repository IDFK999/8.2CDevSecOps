pipeline {
  agent any

  environment {
    NOTIFY = 's221133429@gmail.com'
    // Get the NodeJS tool base dir; we’ll resolve the real bin at runtime
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
# Resolve the actual Node bin directory (handles nested node-v*/bin)
BIN_DIR=""
if [ -d "${NODEJS_HOME}/bin" ]; then
  BIN_DIR="${NODEJS_HOME}/bin"
else
  BIN_DIR=$(ls -d "${NODEJS_HOME}"/node-v*/bin 2>/dev/null | head -n1)
fi
if [ -z "$BIN_DIR" ]; then
  echo "Could not locate Node bin under ${NODEJS_HOME}" >&2
  exit 1
fi

export PATH="$BIN_DIR:$PATH"
echo "Using BIN_DIR=$BIN_DIR"
which node || true
which npm || true
node -v && npm -v
npm install
'''
      }
    }

    stage('Run Tests') {
      steps {
        sh '''
BIN_DIR=""
if [ -d "${NODEJS_HOME}/bin" ]; then
  BIN_DIR="${NODEJS_HOME}/bin"
else
  BIN_DIR=$(ls -d "${NODEJS_HOME}"/node-v*/bin 2>/dev/null | head -n1)
fi
export PATH="$BIN_DIR:$PATH"
npm test || true
'''
      }
      post {
        always {
          emailext(
            to: "${env.NOTIFY}",
            subject: "[SIT223 DevSecOps] Run Tests — ${currentBuild.currentResult} (${env.JOB_NAME} #${env.BUILD_NUMBER})",
            body: "test test — Run Tests stage notification.",
            attachLog: true,
            mimeType: "text/plain"
          )
        }
      }
    }

    stage('Generate Coverage Report') {
      steps {
        sh '''
BIN_DIR=""
if [ -d "${NODEJS_HOME}/bin" ]; then
  BIN_DIR="${NODEJS_HOME}/bin"
else
  BIN_DIR=$(ls -d "${NODEJS_HOME}"/node-v*/bin 2>/dev/null | head -n1)
fi
export PATH="$BIN_DIR:$PATH"
npm run coverage || true
'''
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        sh '''
BIN_DIR=""
if [ -d "${NODEJS_HOME}/bin" ]; then
  BIN_DIR="${NODEJS_HOME}/bin"
else
  BIN_DIR=$(ls -d "${NODEJS_HOME}"/node-v*/bin 2>/dev/null | head -n1)
fi
export PATH="$BIN_DIR:$PATH"
npm audit || true
'''
      }
      post {
        always {
          emailext(
            to: "${env.NOTIFY}",
            subject: "[SIT223 DevSecOps] NPM Audit — ${currentBuild.currentResult} (${env.JOB_NAME} #${env.BUILD_NUMBER})",
            body: "test test — NPM Audit (security scan) stage notification.",
            attachLog: true,
            mimeType: "text/plain"
          )
        }
      }
    }
  }
}


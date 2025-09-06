pipeline {
  agent any

  tools {
    nodejs 'Node 18'   // must match the tool name in Manage Jenkins → Tools
  }

  environment {
    NOTIFY = 's221133429@gmail.com'
  }

  stages {
    stage('Checkout') {
      steps {
        // optional (Jenkins already does an SCM checkout at the top)
        git branch: 'main', url: 'https://github.com/IDFK999/8.2CDevSecOps.git'
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'node -v && npm -v'    // prove NodeJS plugin added Node/npm to PATH
        sh 'npm install'
      }
    }

    stage('Run Tests') {
      steps {
        sh 'npm test || true'
      }
      post {
        always {
          emailext(
            to: "${env.NOTIFY}",
            subject: "[SIT223 DevSecOps] Run Tests — ${currentBuild.currentResult} (${env.JOB_NAME} #${env.BUILD_NUMBER})",
            body: "test test — Run Tests stage notification.",
            attachLog: true,
            mimeType: 'text/plain'
          )
        }
      }
    }

    stage('Generate Coverage Report') {
      steps {
        sh 'npm run coverage || true'
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        sh 'npm audit || true'
      }
      post {
        always {
          emailext(
            to: "${env.NOTIFY}",
            subject: "[SIT223 DevSecOps] NPM Audit — ${currentBuild.currentResult} (${env.JOB_NAME} #${env.BUILD_NUMBER})",
            body: "test test — NPM Audit (security scan) stage notification.",
            attachLog: true,
            mimeType: 'text/plain'
          )
        }
      }
    }
  }
}


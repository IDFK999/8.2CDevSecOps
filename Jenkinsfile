pipeline {
  agent any

  environment {
    NOTIFY = 's221133429@gmail.com'
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/IDFK999/8.2CDevSecOps.git'
      }
    }

    stage('Install Dependencies') {
      steps {
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
            body: "test test — NPM Audit stage notification.",
            attachLog: true,
            mimeType: 'text/plain'
          )
        }
      }
    }
  }
}


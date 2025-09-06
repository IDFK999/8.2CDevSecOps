pipeline {
  agent any
  options { timestamps() }
  // Poll SCM instead of webhook (meets Part 1 trigger requirement)
  triggers { pollSCM('H/5 * * * *') }

  // Make sure your NodeJS tool in "Global Tool Configuration" is named exactly: Node 18
  tools { nodejs 'Node 18' }

  environment {
    EMAIL_TO = 's221133429@gmail.com'
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/IDFK999/8.2CDevSecOps.git'
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'npm ci --no-progress --loglevel=error'
      }
    }

    stage('Run Tests') {
      steps {
        // Succeeds even if tests (Snyk) fail/auth not set — matches brief
        sh 'npm test || true'
      }
      post {
        always {
          emailext(
            to: "${env.EMAIL_TO}",
            subject: "[Tests] ${env.JOB_NAME} #${env.BUILD_NUMBER} - ${currentBuild.currentResult}",
            body: """Build: ${env.BUILD_URL}
Stage: Run Tests
Status: ${currentBuild.currentResult}

Console log attached.""",
            attachLog: true,
            compressLog: true
          )
        }
      }
    }

    stage('Generate Coverage Report') {
      steps {
        sh 'npm run coverage || echo "No coverage script; skipping."'
        script {
          if (fileExists('coverage/lcov.info')) {
            archiveArtifacts artifacts: 'coverage/**', onlyIfSuccessful: false
          }
        }
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        sh '''
          set +e
          npm audit --json > audit.json
          npm audit || true
        '''
      }
      post {
        always {
          emailext(
            to: "${env.EMAIL_TO}",
            subject: "[Security Scan] ${env.JOB_NAME} #${env.BUILD_NUMBER} - ${currentBuild.currentResult}",
            body: """Build: ${env.BUILD_URL}
Stage: NPM Audit (Security Scan)
Status: ${currentBuild.currentResult}

- audit.json attached if present
- Full console log attached.""",
            attachmentsPattern: 'audit.json',
            attachLog: true,
            compressLog: true
          )
        }
      }
    }
  }
}

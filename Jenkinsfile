post {
  always {
    emailext(
      to: 's221133429@gmail.com',
      from: 's221133429@gmail.com',                 // important for Gmail
      replyTo: 's221133429@deakin.edu.au',          // optional
      subject: "[SIT223 DevSecOps] ${env.STAGE_NAME} — ${currentBuild.currentResult} (${env.JOB_NAME} #${env.BUILD_NUMBER})",
      body: "test test — ${env.STAGE_NAME} notification."
      // attachLog: true, compressLog: true   // add back AFTER a successful send
    )
  }
}

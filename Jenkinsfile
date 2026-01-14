pipeline {
    agent any

    environment {
        EMAIL_RECIPIENTS = 'ibchht@gmail.com'
    }

    stages {
        stage('Test Email') {
            steps {
                script {
                    echo '======================================'
                    echo 'Testing Email Notification'
                    echo '======================================'

                    // Test email de succèsdd
                    def successMessage = """
✅ TEST EMAIL - SUCCÈS

Projet: ${env.JOB_NAME}
Build: #${env.BUILD_NUMBER}
Date: ${new Date()}
URL: ${env.BUILD_URL}

Ceci est un email de test.
Si vous recevez cet email, la configuration fonctionne! 🎉

Artefacts (exemple):
- JAR: ${env.BUILD_URL}artifact/
- Javadoc: ${env.BUILD_URL}Javadoc/
                    """

                    emailext(
                        subject: "✅ TEST SUCCESS: ${env.JOB_NAME} - Build #${env.BUILD_NUMBER}",
                        body: successMessage,
                        to: "${EMAIL_RECIPIENTS}",
                        mimeType: 'text/plain'
                    )

                    echo '✓ Email de test envoyé avec succès!'
                }
            }
        }
    }

   post {
       success {
           // Email Notification
           mail to: 'ibchht@gmail.com',
                subject: "Success: ${currentBuild.fullDisplayName}",
                body: "The build and deploy were successful."

           // Slack Notification success
           slackSend color: 'good',
                     channel: 'tp_ogl_gradle', // CHANGE THIS to your actual channel name
                     message: "Build Success: ${currentBuild.fullDisplayName} (<${env.BUILD_URL}|Open>)"
       }
       failure {
           // Email Notification
           mail to: 'ibchht@gmail.com',
                subject: "Failed: ${currentBuild.fullDisplayName}",
                body: "The pipeline failed in stage: ${env.STAGE_NAME}"

           // Slack Notification failure
           slackSend color: 'danger',
                     channel: 'tp_ogl_gradle', // CHANGE THIS to your actual channel name
                     message: "Build Failed: ${currentBuild.fullDisplayName} in stage ${env.STAGE_NAME} (<${env.BUILD_URL}|Open>)"
       }
   }
   }
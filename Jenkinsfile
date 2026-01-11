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
            script {
                echo '======================================'
                echo 'Email SUCCESS envoyé depuis post block'
                echo '======================================'

                emailext(
                    subject: "✅ POST SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                    body: """
✅ NOTIFICATION DE SUCCÈS (POST BLOCK)

Le build s'est terminé avec succès!

Projet: ${env.JOB_NAME}
Build: #${env.BUILD_NUMBER}
Durée: ${currentBuild.durationString}

Ce message vient du bloc 'post { success }'
                    """,
                    to: "${EMAIL_RECIPIENTS}"
                )
            }
        }

        failure {
            script {
                echo '======================================'
                echo 'Email FAILURE envoyé depuis post block'
                echo '======================================'

                emailext(
                    subject: "❌ POST FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                    body: """
❌ NOTIFICATION D'ÉCHEC (POST BLOCK)

Le build a échoué!

Projet: ${env.JOB_NAME}
Build: #${env.BUILD_NUMBER}
Phase échouée: ${env.STAGE_NAME}

Logs: ${env.BUILD_URL}console

Intervention requise! 🚨
                    """,
                    to: "${EMAIL_RECIPIENTS}",
                    attachLog: true
                )
            }
        }

        always {
            script {
                echo "Build terminé avec statut: ${currentBuild.result ?: 'SUCCESS'}"
            }
        }
    }
}
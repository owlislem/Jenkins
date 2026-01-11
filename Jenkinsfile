pipeline {
    agent any

    stages {
        stage('Test') {
            steps {
                script {
                    echo 'Lancement des tests unitaires...'
                    bat 'gradlew.bat clean test'

                    echo 'Archivage des résultats des tests unitaires...'
                    junit 'build/test-results/test/*.xml'

                    echo 'Génération des rapports Cucumber...'
                    bat 'gradlew.bat generateCucumberReports'

                    publishHTML(target: [
                        reportName: 'Cucumber Report',
                        reportDir: 'build/reports/cucumber/cucumber-html-reports',
                        reportFiles: 'overview-features.html',
                        keepAll: true,
                        alwaysLinkToLastBuild: true,
                        allowMissing: false
                    ])
                }
            }
        }

        stage('Code Analysis') {
            steps {
                script {
                    echo 'Analyse du code avec SonarQube....'
                    withSonarQubeEnv('MySonarQubeServer') {
                        bat 'gradlew.bat sonarqube'
                    }
                }
            }
        }

        stage('Code Quality') {
            steps {
                script {
                    echo 'Vérification du Quality Gate...'
                    def qg = waitForQualityGate() // bloque jusqu'à cdsade que SonarQube ait fini
                    if (qg.status != 'OK') {
                        error "Quality Gate failed: ${qg.status}"
                    }
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    echo 'Génération du JAR, documentation et archivage...'
                    bat 'gradlew.bat jar javadoc archiveBuild'

                    echo 'Les fichiers JAR et la documentation ont été archivés dans build/archive.'
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    echo 'Déploiement du JAR sur mymavenrepo.com...'
                    bat 'gradlew.bat publish'
                    echo 'Déploiement terminé avec succès.'
                }
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline réussi !'

            emailext (
                subject: "✅ Build Réussi - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
                    <h2>✅ Build Réussi</h2>
                    <p><b>Projet :</b> ${env.JOB_NAME}</p>
                    <p><b>Build n° :</b> ${env.BUILD_NUMBER}</p>
                    <p><b>Date :</b> ${new Date()}</p>
                    <p><a href="${env.BUILD_URL}">Voir les détails du build</a></p>
                """,
                to: 'amiryeld@gmail.com',   // <- your recipient
                from: 'amiryeld@gmail.com', // <- must match your SMTP account
                mimeType: 'text/html'
            )
        }

        failure {
            echo '❌ Pipeline échoué !'

            emailext (
                subject: "❌ Build Échoué - ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
                    <h2>❌ Build Échoué</h2>
                    <p><b>Projet :</b> ${env.JOB_NAME}</p>
                    <p><b>Build n° :</b> ${env.BUILD_NUMBER}</p>
                    <p><b>Erreur :</b> Une ou plusieurs étapes ont échoué.</p>
                    <p><a href="${env.BUILD_URL}console">Voir les logs complets</a></p>
                """,
                to: 'amiryeld@gmail.com',   // <- your recipient
                from: 'amiryeld@gmail.com', // <- must match your SMTP account
                mimeType: 'text/html'
            )
        }
    }

}

pipeline {
    agent any

    environment {
        MAVEN_REPO_URL = "https://mymavenrepo.com/repo/cEmjfkxugPlzLxXg1A2B/"
        EMAIL_FROM = "amiryeld@gmail.com"
        EMAIL_TO = "amiryeld@gmail.com"
    }

    stages {

        stage('Test') {
            steps {
                echo '🏃 Lancement des tests unitaires...'
                bat 'gradlew.bat clean test'

                echo '📂 Archivage des résultats des tests unitaires...'
                junit 'build/test-results/test/*.xml'

                echo '📊 Génération des rapports Cucumber...'
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

        stage('Code Analysis') {
            steps {
                echo '🔍 Analyse du code avec SonarQube...'
                withSonarQubeEnv('MySonarQubeServer') {
                    bat 'gradlew.bat sonarqube'
                }
            }
        }

        stage('Code Quality') {
            steps {
                echo '📈 Vérification du Quality Gate...'
                script {
                    def qg = waitForQualityGate()
                    if (qg.status != 'OK') {
                        error "Quality Gate failed: ${qg.status}"
                    }
                }
            }
        }

        stage('Build') {
            steps {
                echo '⚙️ Génération du JAR et de la documentation...'
                bat 'gradlew.bat jar javadoc'

                echo '📦 Archivage du JAR et Javadoc...'
                bat 'gradlew.bat archiveBuild'
            }
        }

        stage('Deploy') {
            steps {
                echo '🚀 Déploiement sur Maven repository...'
                withCredentials([usernamePassword(credentialsId: 'MY_MAVEN_CREDS', usernameVariable: 'MAVEN_REPO_USERNAME', passwordVariable: 'MAVEN_REPO_PASSWORD')]) {
                    bat 'gradlew.bat publish'
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
                to: EMAIL_TO,
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
                to: EMAIL_TO,
                mimeType: 'text/html'
            )
        }
    }
}

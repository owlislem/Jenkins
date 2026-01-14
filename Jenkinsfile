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
                    def qg = waitForQualityGate() // bloque jusqu'à ce que SonarQube ait fini
                    if (qg.status != 'OK') {
                        error "Quality Gate failed: ${qg.status}"
                    }
                }
            }
        }

        stage('Build') {
            steps {
                // 1. Génération du fichier Jar
                bat 'gradlew assemble'

                // 2. Génération de la documentation
                bat 'gradlew javadoc'
            }
            post {
                success {
                    // 3. Archivage du fichier Jar et de la documentation
                    archiveArtifacts artifacts: 'build/libs/*.jar, build/docs/javadoc/**'
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
            // Email Notification
            mail to: 'ibchht@gmail.com',
                 subject: "Success: ${currentBuild.fullDisplayName}",
                 body: "The build and deploy were successful."

            // Slack Notification success
            slackSend color: 'good',
                      channel: 'tous-test-jenkins', // CHANGE THIS to your actual channel name
                      message: "Build Success: ${currentBuild.fullDisplayName} (<${env.BUILD_URL}|Open>)"
        }
        failure {
            // Email Notification
            mail to: 'ibchht@gmail.com',
                 subject: "Failed: ${currentBuild.fullDisplayName}",
                 body: "The pipeline failed in stage: ${env.STAGE_NAME}"

            // Slack Notification failhure
            slackSend color: 'danger',
                      channel: 'tous-test-jenkins', // CHANGE THIS to your actual channel name
                      message: "Build Failed: ${currentBuild.fullDisplayName} in stage ${env.STAGE_NAME} (<${env.BUILD_URL}|Open>)"
        }
    }
}
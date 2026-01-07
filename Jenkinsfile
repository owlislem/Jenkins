pipeline {
    agent any

    stages {
        // =========================
        // 1️⃣ TEST UNITAIRES + CUCUMBER
        // =========================
        stage('Test') {
            steps {
                script {
                    echo 'Lancement des tests unitaires...'
                    bat 'gradlew.bat clean test'

                    echo 'Archivage des résultats des tests unitaires...'
                    junit 'build/test-results/test/*.xml'

                    echo 'Génération des rapports Cucumber...'
                    bat 'gradlew.bat generateCucumberReports'

                    echo 'Publication des rapports Cucumber dans Jenkins...'
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

        // =========================
        // 2️⃣ ANALYSE SONARQUBE
        // =========================
        stage('Code Analysis') {
            steps {
                script {
                    echo 'Analyse du code avec SonarQube...'
                    // Utilise withSonarQubeEnv pour lier ton serveur configuré dans Jenkins
                    withSonarQubeEnv('MySonarQubeServer') { // Remplace par le nom de ton serveur Sonar
                        bat 'gradlew.bat sonarqube'
                    }
                }
            }
            post {
                failure {
                    echo "Code Analysis failed. Check SonarQube report."
                }
            }
        }

        // =========================
        // 3️⃣ VERIFICATION QUALITY GATE
        // =========================
        stage('Code Quality') {
            steps {
                script {
                    echo 'Vérification du Quality Gate...'
                    def qg = waitForQualityGate()
                    if (qg.status != 'OK') {
                        error "Quality Gate failed: ${qg.status}"
                    }
                }
            }
        }
    }
}

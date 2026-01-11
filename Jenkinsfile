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

        stage('Build') {
            steps {
                script {
                    echo 'Génération du JAR...'
                    bat 'gradlew.bat jar'

                    echo 'Génération de la documentation Javadoc...'
                    bat 'gradlew.bat javadoc'

                    echo 'Archivage du JAR et de la documentation...'
                    bat 'gradlew.bat archiveBuild' // your Gradle task

                    echo 'Archiver dans Jenkins...'
                    archiveArtifacts artifacts: 'build/distributions/*.zip', fingerprint: true
                }
            }
        }

        stage('Code Analysis') {
            steps {
                script {
                    echo 'Analyse du code avec SonarQube...'
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
    }
}

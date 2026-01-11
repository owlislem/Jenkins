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
                    // Lier ton serveur Jenkins SonarQubedaa
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
                    def qg = waitForQualityGate() // bloque jusqu'à ce que SonarQube aitf
                    if (qg.status != 'OK') {
                        error "Quality Gate failed: ${qg.status}"
                    }
                }
            }
            stage('Build') {
                steps {
                    script {
                        echo '======================================'
                        echo 'Step 4: Build Phase'
                        echo '======================================'

                        // Step 1: Generate JAR fileddd
                        echo 'Génération du fichier JAR...'
                        bat 'gradlew.bat clean build -x test'

                        // Step 2: Generate Javadoc documentation
                        echo 'Génération de la documentation Javadoc...'
                        bat 'gradlew.bat javadoc'

                        // Step 3: Archive JAR and documentation
                        echo 'Archivage du fichier JAR et de la documentation...'

                        // Archive JAR file
                        archiveArtifacts artifacts: 'build/libs/*.jar',
                                       fingerprint: true,
                                       allowEmptyArchive: false

                        // Archive Javadoc documentation
                        archiveArtifacts artifacts: 'build/docs/javadoc/**/*',
                                       fingerprint: true,
                                       allowEmptyArchive: false

                        // Publish Javadoc as HTML report
                        publishHTML(target: [
                            reportName: 'Javadoc',
                            reportDir: 'build/docs/javadoc',
                            reportFiles: 'index.html',
                            keepAll: true,
                            alwaysLinkToLastBuild: true,
                            allowMissing: false
                        ])

                        echo '✓ Build phase completed successfully'
                    }
                }
                post {
                    success {
                        echo 'JAR et documentation générés et archivés avec succès'
                    }
                    failure {
                        echo 'Échec de la phase Build'
                        error('Build phase failed')
                    }
                }
            }

        }
    }
}

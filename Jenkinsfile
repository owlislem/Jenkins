pipeline {
    agent any

    environment {
        // Maven repository URL
        MAVEN_REPO_URL = "https://mymavenrepo.com/repo/cEmjfkxugPlzLxXg1A2B/"
    }

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
                    def qg = waitForQualityGate() // Bloque jusqu'à ce que SonarQube ait fini
                    if (qg.status != 'OK') {
                        error "Quality Gate failed: ${qg.status}"
                    }
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    echo 'Génération du fichier JAR...'
                    bat 'gradlew.bat jar'

                    echo 'Génération de la documentation...'
                    bat 'gradlew.bat javadoc'

                    echo 'Archivage du fichier JAR et de la documentation...'
                    bat 'gradlew.bat archiveBuild'

                    // Optionally, publish archive as artifact in Jenkins
                    archiveArtifacts artifacts: 'build/archive/**', fingerprint: true
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    echo 'Déploiement sur Maven repository...'
                    withCredentials([usernamePassword(credentialsId: 'MY_MAVEN_CREDS', usernameVariable: 'MAVEN_REPO_USERNAME', passwordVariable: 'MAVEN_REPO_PASSWORD')]) {
                        // Gradle will read MAVEN_REPO_URL, MAVEN_REPO_USERNAME, MAVEN_REPO_PASSWORD
                        bat 'gradlew.bat publish -PmavenRepoUrl=%MAVEN_REPO_URL% -PmavenUsername=%MAVEN_REPO_USERNAME% -PmavenPassword=%MAVEN_REPO_PASSWORD%'
                    }
                }
            }
        }
    }
}

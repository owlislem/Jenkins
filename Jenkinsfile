pipeline {
    agent any

    stages {
        stage('Test') {
            steps {
                script {
                    echo 'Lancement des tests unitaires...'
                    bat 'gradlew.bat clean test'  // sur Windows

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
    }
}

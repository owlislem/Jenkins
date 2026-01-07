pipeline {
    agent any

    stages {
        stage('Test') {
            steps {
                script {
                    echo 'Lancement des tests unitaires...'

                    bat 'gradlew.bat test'



                    echo 'Archivage des résultats des tests unitaires...'
                    junit 'build/test-results/test/*.xml'

                    echo 'Génération des rapports Cucumber...'
                    cucumber buildStatus: true,
                             fileIncludePattern: 'build/reports/cucumber/*.json',
                             jsonReportDirectory: 'build/reports/cucumber/',
                             pluginDirectory: 'build/reports/cucumber/cucumber-html-reports'
                }
            }
        }
    }
}

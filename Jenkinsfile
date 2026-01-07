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

   stage('Code Analysis') {
       steps {
           script {
               echo 'Analyse du code avec SonarQube...'
               // Utilise le wrapper Jenkins SonarQube
               withSonarQubeEnv('MySonarQubeServer') { // 'MySonarQubeServer' = Nom configuré dans Jenkins
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

    }
}

pipeline {
    agent any

    triggers {
        githubPush()   
    }

    stages {

        stage('Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/Oymane/cargo-tracker-UM6P1'
            }
        }

        stage('Build & Test with Coverage') {
            steps {
                sh 'chmod +x mvnw'
                sh './mvnw clean verify'
            }
        }

        stage('SonarQube Analysis') {
            environment {
                SONAR_TOKEN = credentials('sonar-token-id')
            }
            steps {
                withSonarQubeEnv('SonarQube Local') {
                    sh """
                        ./mvnw sonar:sonar \\
                        -Dsonar.projectKey=cargo-tracker \\
                        -Dsonar.projectName="Cargo Tracker" \\
                        -Dsonar.coverage.jacoco.xmlReportPaths=target/site/jacoco/jacoco.xml \\
                        -Dsonar.host.url=http://sonarqube:9000 \\
                        -Dsonar.token=${SONAR_TOKEN}
                    """
                }
            }
        }
    }
//
    post {
        success {
            echo 'Build et analyse terminés avec succès !'
        }
        failure {
            echo 'Échec du build ou des tests.'
        }
    }
}

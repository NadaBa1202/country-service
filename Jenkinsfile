pipeline {
    agent any
    tools {
        jdk 'JDK21'
        maven 'M2_HOME'
    }
    stages {
        stage('Checkout code') {
            steps {
                git branch: 'main', url: 'https://github.com/NadaBa1202/country-service.git'
            }
        }
        stage('Compile code') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('Test code') {
            steps {
                sh 'mvn test'
            }
            post {
                success {
                    junit allowEmptyResults: true, testResults: '**/target/surfire-reports/*.xml'
                }
            }
        }
        stage('Package code') {
            steps {
                sh 'mvn package'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv(installationName: 'MySonarQubeServer', credentialsId: 'sonarqubePWD') {
                    sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:sonar -Dsonar.projectKey=country-service -Dsonar.projectName=country-service'
                }
            }
        }
    }
}

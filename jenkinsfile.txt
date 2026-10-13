pipeline {
    agent any
    tools {
        maven 'mymaven'
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
    }
}
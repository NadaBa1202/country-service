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
                stage('Deploy jar to Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'nexus-credentials',
                        usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
                    sh 'mvn deploy -DskipTests'
                }
            }
        }
        stage('Upload reports to Nexus') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'nexus-credentials',
                        usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
                    sh '''
                        cd target
                        tar -czf jacoco-report.tar.gz site/jacoco
                        tar -czf surefire-reports.tar.gz surefire-reports
                        BASE=http://localhost:8081/repository/reports/country-service/${BUILD_NUMBER}
                        curl --fail -u "$NEXUS_USER:$NEXUS_PASS" --upload-file jacoco-report.tar.gz $BASE/jacoco-report.tar.gz
                        curl --fail -u "$NEXUS_USER:$NEXUS_PASS" --upload-file surefire-reports.tar.gz $BASE/surefire-reports.tar.gz
                    '''
                }
            }
        }
    }
}

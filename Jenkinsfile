pipeline {
    agent any

    tools {
        maven 'Maven-3.9.12'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/master']],
                    userRemoteConfigs: [[
                        credentialsId: 'github-credentials',
                        url: 'https://github.com/balagithub98/sample-war-project.git'
                    ]]
                ])
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
                sh 'ls -l target'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('Sonarqube-8.0') {
                    withCredentials([string(credentialsId: 'sonarqube-credentials', variable: 'SONAR_TOKEN')]) {
                        sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=sample-war-project -Dsonar.projectName=sample-war-project -Dsonar.host.url=http://13.126.185.160:9000 -Dsonar.token=${SONAR_TOKEN}'
                    }
                }
            }
        }
    }
}


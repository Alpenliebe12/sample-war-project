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
                    sh 'mvn sonar:sonar -Dsonar.projectKey=sample-war-project -Dsonar.projectName=sample-war-project'
                }
            }
        }

        stage('Nexus Upload') {
            steps {
                nexusArtifactUploader(
                    artifacts: [[
                        artifactId: 'sample-war-project',
                        classifier: '',
                        file: 'target/sample-war-project.war',
                        type: 'war'
                    ]],
                    credentialsId: 'nexus-credential',
                    groupId: 'com.example.web',
                    nexusUrl: '3.108.54.141:8081',
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    repository: 'sample-war-project-snapshot',
                    version: '1.0-SNAPSHOT'
                )
            }
        }
    }
}


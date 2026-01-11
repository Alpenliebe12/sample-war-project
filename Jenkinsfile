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
                        url: 'https://github.com/Alpenliebe12/demo-war-project.git'
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
                    sh 'mvn sonar:sonar -Dsonar.projectKey=demo-war-project -Dsonar.projectName=demo-war-project'
                }
            }
        }

        stage('Nexus Upload') {
            steps {
                nexusArtifactUploader(
                    artifacts: [[
                        artifactId: 'demo-war-project',
                        classifier: '',
                        file: 'target/demo-war-project.war',
                        type: 'war'
                    ]],
                    credentialsId: 'nexus-credential',
                    groupId: 'com.example.web',
                    nexusUrl: '3.108.54.141:8081',
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    repository: 'demo-war-project-snapshot',
                    version: '1.0-SNAPSHOT'
                )
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                sshagent(['tomcat-ssh-agent']) {
                    sh 'scp -o StrictHostKeyChecking=no target/demo-war-project.war ubuntu@3.108.219.162:/tmp/'
                    sh 'ssh -o StrictHostKeyChecking=no ubuntu@3.108.219.162 "sudo mv /tmp/demo-war-project.war /opt/tomcat/apache-tomcat-10.1.49/webapps/demo-war-project.war"'
                }
            }
        }
    }
}


pipeline {
    agent any

    tools {
        jdk 'jdk17'
        maven 'maven'
    }

    stages {

        stage('git clone') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/shriramkhardekar/maven-web-app.git'
            }
        }

        stage('maven build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-9.9.8') {
                    sh 'mvn sonar:sonar'
                }
            }
        }   // ✅ Sonar stage closed properly

        stage('build docker image') {
            steps {
                script {
                    sh 'docker build -t myapp:latest .'
                }
            }
        }

        stage('k8s deploy') {
            steps {
                sh 'kubectl apply -f deploy-k8s.yml'
            }
        }

    }   // stages closed
}   // pipeline closed

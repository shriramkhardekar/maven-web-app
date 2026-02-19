pipeline {
    agent any
    tools{
    jdk 'jdk17'
    maven 'maven'
}
    stages {
        stage('git clone') {
            steps {
             git branch: 'main', url: 'https://github.com/shriramkhardekar/maven-web-app.git'
            }
        }
        stage('maven build') {
            steps {
             sh 'mvn clean package'
            }
        }
       stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('MySonarServer') {   // <--- notice the closure {}
                    sh 'mvn sonar:sonar'
                }
            }
        stage('build docker image'){
            steps{
                 script {
                    // Build Docker image, tag it with 'myapp:latest'
                    sh 'docker build -t myapp:latest .'
                }
            }
        }
        stage('k8s deploy'){
            steps{
                sh 'kubectl apply -f deploy-k8s.yml'
            }
        }
            }
        }



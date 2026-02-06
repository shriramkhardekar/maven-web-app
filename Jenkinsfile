pipeline {
    agent any
    tools{
    jdk 'jdk'
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
        stage('build docker image'){
            steps{
                 script {
                    // Build Docker image, tag it with 'myapp:latest'
                    sh 'docker build -t myapp:latest .'
                }
            }
        }
            }
        }



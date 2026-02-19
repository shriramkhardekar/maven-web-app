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
        stage('SonarQube analysis') {
			steps{
		 withSonarQubeEnv('Sonar-9.9.8')
	     sh "mvn sonar:sonar"
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



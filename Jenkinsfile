pipeline {
    agent any

    tools {
        jdk 'jdk17'
        maven 'maven'
    }

    environment {
        DOCKER_HUB_USER = 'shriramk02'          // Docker Hub username
        DOCKER_HUB_CRED = '9449d280-ceba-4bb6-a973-0d8a97cf0d7f'    // Jenkins credential ID
        IMAGE_NAME     = 'myapp'                       // Docker image name
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
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image and tag with Jenkins build number
                    sh "docker build -t ${DOCKER_HUB_USER}/${IMAGE_NAME}:${BUILD_NUMBER} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Login to Docker Hub using Jenkins credentials and push image
                    withCredentials([usernamePassword(credentialsId: "${DOCKER_HUB_CRED}", 
                                                     usernameVariable: 'DOCKER_USER', 
                                                     passwordVariable: 'DOCKER_PASS')]) {
                        sh """
                            echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                            docker push ${DOCKER_HUB_USER}/${IMAGE_NAME}:${BUILD_NUMBER}
                            docker push ${DOCKER_HUB_USER}/${IMAGE_NAME}:latest
                        """
                    }
                }
            }
        }

        stage('k8s deploy') {
            steps {
                script {
                    // Deploy to EKS using the image with the current build number
                    sh """
                        kubectl set image deployment/tomcat-deployment \
                        tomcat=${DOCKER_HUB_USER}/${IMAGE_NAME}:${BUILD_NUMBER} --record
                        kubectl rollout status deployment/tomcat-deployment
                    """
                }
            }
        }

    }   // stages closed
}   // pipeline closed

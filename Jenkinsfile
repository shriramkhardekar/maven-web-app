pipeline {
    agent any

    tools {
        jdk 'jdk17'
        maven 'maven'
    }

    environment {
        DOCKER_HUB_USER = 'shriramk02'                          // Docker Hub username
        DOCKER_HUB_CRED = '9449d280-ceba-4bb6-a973-0d8a97cf0d7f' // Jenkins credential ID
        IMAGE_NAME     = 'myapp'                                 // Docker image name
        K8S_DEPLOYMENT = 'mavenwebappdeployment'                // Kubernetes deployment name
        K8S_CONTAINER  = 'mavenwebappcontainer'                 // Container name in deployment
        K8S_NAMESPACE  = 'default'                              // Namespace (change if needed)
    }

    stages {

        stage('Git Clone') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/shriramkhardekar/maven-web-app.git'
            }
        }

        stage('Maven Build') {
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
                    // Build Docker image tagged with Jenkins build number
                    sh "docker build -t ${DOCKER_HUB_USER}/${IMAGE_NAME}:${BUILD_NUMBER} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: "${DOCKER_HUB_CRED}", 
                        usernameVariable: 'DOCKER_USER', 
                        passwordVariable: 'DOCKER_PASS')]) {
                        sh """
                            echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                            docker push ${DOCKER_HUB_USER}/${IMAGE_NAME}:${BUILD_NUMBER}
                        """
                    }
                }
            }
        }

        stage('K8s Deploy') {
            steps {
                script {
                    // Update Kubernetes deployment to use the new image tagged with BUILD_NUMBER
                    sh """
                        kubectl set image deployment/${K8S_DEPLOYMENT} \
                        ${K8S_CONTAINER}=${DOCKER_HUB_USER}/${IMAGE_NAME}:${BUILD_NUMBER} \
                        --namespace=${K8S_NAMESPACE} --record
                        
                        kubectl rollout status deployment/${K8S_DEPLOYMENT} \
                        --namespace=${K8S_NAMESPACE}
                    """
                }
            }
        }

    } // stages closed
} // pipeline closed

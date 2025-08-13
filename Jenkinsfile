pipeline {
  agent any
       environment {
          DOCKER_HUB_USERNAME = credentials('dockerhub-credentials').getUsername()
          DOCKER_HUB_PASSWORD = credentials('dockerhub-credentials').getPassword()
           IMAGE_NAME = 'cicd-app'
             IMAGE_TAG = 'latest'
             KUBERNETES_DEPLOYMENT_NAME = 'cicd-app-deployment'
            KUBERNETES_SERVICE_NAME = 'cicd-app-service'
        }
   
        stages {
             stage('Checkout Code') {
                steps {
                    script {
                        git branch: 'main', url: 'https://github.com/imranber/cicd-project.git' // Your GitHub
     repo URL
                    }
                }
            }
   
           
                 stage('Build Docker Image') {
                     steps {
                         withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials',
      usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                             script {
                                 // Ensure Docker is available and build the image
                                 sh "docker build -t ${DOCKER_USER}/${IMAGE_NAME}:${IMAGE_TAG} ."
                             }
                         }
                     }
                }
   
             stage('Push Docker Image') {
                     steps {
                         withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials',
      usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                             script {
                                 // Login to Docker Hub
                                 sh "echo -n ${DOCKER_PASS} | docker login -u ${DOCKER_USER} --password-stdin"
                                 // Push the image
                                 sh "docker push ${DOCKER_USER}/${IMAGE_NAME}:${IMAGE_TAG}"
                                 // Logout from Docker Hub
                                sh "docker logout"
                            }
                        }
                    }
                }
   
            stage('Deploy to Kubernetes') {
                steps {
                    script {
                        // Use minikube's kubectl to apply manifests
                        sh "minikube kubectl -- apply -f k8s-manifests/deployment.yaml"
                        sh "minikube kubectl -- apply -f k8s-manifests/service.yaml"
   
                        // Optional: Rollout status check
                        sh "minikube kubectl -- rollout status deployment/${KUBERNETES_DEPLOYMENT_NAME}"
                    }
                }
            }
        }
    }

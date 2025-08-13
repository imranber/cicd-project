pipeline {
  agent any
       environment {
           IMAGE_NAME = 'cicd-app'
             IMAGE_TAG = 'latest'
             KUBERNETES_DEPLOYMENT_NAME = 'cicd-app-deployment'
            KUBERNETES_SERVICE_NAME = 'cicd-app-service'
        }
   
        stages {
             stage('Checkout Code') {
                steps {
                    script {
                        git branch: 'main', url: 'https://github.com/imranber/cicd-project.git' 
     
                    }
                }
            }
   
           
                 stage('Build Docker Image') {
                     steps {
                         withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials',
      usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                             script {
                                 
                                 sh "docker build --no-cache -t ${DOCKER_USER}/${IMAGE_NAME}:${IMAGE_TAG} ."
                             }
                         }
                     }
                }
   
             stage('Push Docker Image') {
                     steps {
                         withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials',
      usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                             script {
                                 
                                   sh 'echo -n "${DOCKER_PASS}" | docker login -u "${DOCKER_USER}" --password-stdin'
                            
                                 sh "docker push ${DOCKER_USER}/${IMAGE_NAME}:${IMAGE_TAG}"
                                
                                sh "docker logout"
                            }
                        }
                    }
                }
   
            stage('Deploy to Kubernetes') {
                steps {
                    script {
                       
                        sh "minikube kubectl -- apply -f k8s-manifests/deployment.yaml"
                        sh "minikube kubectl -- apply -f k8s-manifests/service.yaml"
   
                       
                        sh "minikube kubectl -- rollout status deployment/${KUBERNETES_DEPLOYMENT_NAME}"
                    }
                }
            }
        }
    }

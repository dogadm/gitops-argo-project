pipeline{


    agent any

    environment{

        DOCKERHUB_USERNAME = "dogadm"
        APP_NAME = "gitops-argo-app"
        IMAGE_TAG = "${BUILD_NUMBER}"
        IMAGE_NAME = "${DOCKERHUB_USERNAME}" + "/${APP_NAME}"
        REGISTRY_CREDS = 'dockerhub'
    }

    stages{

        stage('Clean workspace'){

            steps{
                script{

                    cleanWs()
                }
            }

        }

        stage('Checkout SCM'){

            steps{
                script{
                    git credentialsId: 'github',
                    url: 'https://github.com/dogadm/gitops-argo-project1.git',
                    branch: 'main' 
                }
            }
        }
        
        stage('Build Docker Image'){
            
            steps{
                script{

                    docker_image = docker.build "${IMAGE_NAME}"
                }
            }
        }
        stage('Push Docker Image'){

            steps{
                script{

                    docker.withRegistry('',REGISTRY_CREDS){
                        docker_image.push("$BUILD_NUMBER")
                        docker_image.push('latest')
                    }
                }
            }
        }

        stage('Delete Docker Images'){

            steps{
                script{

                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker rmi ${IMAGE_NAME}:latest"
                }

            }

        }
        stage('Updating Kubernetes Deployment file'){

            steps{
                script{

                    sh"""
                    cat deployment.yaml
                    sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' deployment.yml
                    cat deployment.yml

                    """
                }
            }''
        }
    }
}
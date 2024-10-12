pipeline {
    agent {
        kubernetes {
            inheritFrom 'base-image'  // Reference to the pod template label
            defaultContainer 'base-image-container'
        }
    }
    environment {
        AWS_REGION = 'us-east-1' // Set your AWS region
        ECR_REPO = 'default/hello-world' // Set your ECR repository name
        AWS_ACCOUNT_ID = "047125666935"
        URL_REGISTRY = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
    }
    stages {
        stage('setup environment') {
            steps {
                script {
                    sh label: "make sure the docker engine has started", script :
                    """
                        unset DOCKER_HOST
                        docker context use default
                        dockerd &
                    """
                    

                }
            }
        }
        stage('build') {
            steps {
                // Build the Docker image
                script {
                    env.imageName = 'hello-world'
                    env.imageTag = 'latest'
                    docker.build("${env.imageName}:${env.imageTag}")
                }
            }
        }
        stage('push image to ECR') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'aws-credentials', usernameVariable: 'AWS_ACCESS_KEY_ID', passwordVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                        sh label: "login and push to ECR", script :
                        """
                            aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${URL_REGISTRY}
                            docker tag ${env.imageName}:${env.imageTag} ${URL_REGISTRY}/${ECR_REPO}:latest
                            docker push ${URL_REGISTRY}/${ECR_REPO}:latest
                        """
                    }
                }
            }
        }
        stage('install helm') {
            steps {
                script {
                    sh label: "install hello-world-app helm", script: "helm upgrade hello-world-app ./hello-world-app"
                    sh label: "run mendatory actions for the app to work", script :
                    '''
                        NODE_PORT=$(kubectl get --namespace default -o jsonpath="{.spec.ports[0].nodePort}" services hello-world-app-service)
                        NODE_IP=$(kubectl get nodes --namespace default -o jsonpath="{.items[0].status.addresses[0].address}")
                        echo "http://$NODE_IP:$NODE_PORT"
                    '''
                }
            }
        }
    }
}

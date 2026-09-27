pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'gopala1612/devops-web:${BUILD_NUMBER}'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Docker Version') {
            steps {
                sh 'docker --version'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Docker Image Check') {
            steps {
                sh 'docker images | grep gopala1612/devops-web'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin
                        docker push $DOCKER_IMAGE
                        docker logout
                    '''
                }
            }
        }
		stage('Deploy to Kubernetes with Helm') {
            steps {
                sh '''
                    echo "Deploying application to Kubernetes using Helm..."
        
                    helm upgrade --install devops-web-helm ./helm/devops-web \
                      --namespace default \
                      --create-namespace \
                      --set image.tag=${BUILD_NUMBER}
        
                    kubectl rollout status deployment/devops-web-helm-devops-web
        
                    echo "Helm release status:"
                    helm status devops-web-helm
        
                    echo "Kubernetes resources:"
                    kubectl get pods -o wide
                    kubectl get svc
                    kubectl get ingress
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Docker build, Docker Hub push and Helm-based Kubernetes deployment completed successfully!'
        }

        failure {
            echo '❌ Pipeline failed. Check the console output.'
        }
    }
}


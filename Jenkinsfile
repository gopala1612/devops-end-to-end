pipeline {
    agent any

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
                sh '''
                    docker build -t gopala1612/devops-web:v1 .
                '''
            }
        }

        stage('Docker Image Check') {
            steps {
                sh '''
                    docker images | grep gopala1612/devops-web
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Jenkins pipeline completed successfully!'
        }

        failure {
            echo '❌ Jenkins pipeline failed. Check the console output.'
        }
    }
}

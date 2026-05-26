pipeline {
    agent any

    environment {
        IMAGE_NAME = "981629166290.dkr.ecr.eu-north-1.amazonaws.com/sample-app"
        AWS_REGION = "eu-north-1"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                url: 'https://github.com/vaidehipipare/sample-devops-project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t sample-app .'
            }
        }

        stage('Tag Docker Image') {
            steps {
                sh 'docker tag sample-app:latest $IMAGE_NAME:latest'
            }
        }

        stage('Login to AWS ECR') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws-creds',
                    accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                    secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
                ]]) {

                    sh '''
                    aws ecr get-login-password --region $AWS_REGION | \
                    docker login --username AWS \
                    --password-stdin 981629166290.dkr.ecr.eu-north-1.amazonaws.com
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh 'docker push $IMAGE_NAME:latest'
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh 'kubectl apply -f k8s/deployment.yaml'
                sh 'kubectl apply -f k8s/service.yaml'
            }
        }
    }
}

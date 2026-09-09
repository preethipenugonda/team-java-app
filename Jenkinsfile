pipeline {

    agent any

    parameters {
        string(
            name: 'IMAGE_TAG',
            defaultValue: '1.2',
            description: 'Docker image tag (example: 1.0, 1.1, 1.2)'
        )
    }

    environment {
        AWS_REGION = 'ap-south-1'
        ECR_REGISTRY = '446677926161.dkr.ecr.ap-south-1.amazonaws.com'
        ECR_REPOSITORY = 'team-java-app'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Maven Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                    docker build \
                    -t ${ECR_REPOSITORY}:${IMAGE_TAG} .
                '''
            }
        }

        stage('ECR Login') {
            steps {
                sh '''
                    aws ecr get-login-password --region ${AWS_REGION} | \
                    docker login --username AWS --password-stdin ${ECR_REGISTRY}
                '''
            }
        }

        stage('Docker Tag') {
            steps {
                sh '''
                    docker tag \
                    ${ECR_REPOSITORY}:${IMAGE_TAG} \
                    ${ECR_REGISTRY}/${ECR_REPOSITORY}:${IMAGE_TAG}
                '''
            }
        }

        stage('Docker Push') {
            steps {
                sh '''
                    docker push \
                    ${ECR_REGISTRY}/${ECR_REPOSITORY}:${IMAGE_TAG}
                '''
            }
        }
    }

    post {
        success {
            echo "Image ${IMAGE_TAG} pushed successfully to ECR."
        }

        failure {
            echo "Pipeline failed."
        }
    }
}

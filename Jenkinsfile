pipeline {
agent any


environment {
    IMAGE_NAME = "raguraaman/trend-app-proj2"
    IMAGE_TAG = "${BUILD_NUMBER}"
    AWS_DEFAULT_REGION = "ap-south-1"
}

stages {

    stage('Checkout') {
        steps {
            checkout scm
        }
    }

    stage('Build Docker Image') {
        steps {
            sh """
                docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
            """
        }
    }

    stage('Push Docker Image') {
        steps {
            withCredentials([
                usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )
            ]) {
                sh """
                    echo \$DOCKER_PASS | docker login -u \$DOCKER_USER --password-stdin
                    docker push ${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }
    }

    stage('Deploy to EKS') {
        steps {
            sh """
                export AWS_PAGER=""

                aws eks update-kubeconfig \
                --region ${AWS_DEFAULT_REGION} \
                --name trend-eks

                kubectl rollout restart deployment/trend-app

                kubectl rollout status deployment/trend-app --timeout=300s
            """
        }
    }
}

post {
    success {
        echo 'Pipeline completed successfully'
    }

    failure {
        echo 'Pipeline failed'
    }
}


}


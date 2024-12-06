pipeline {
    agent any
    environment {
        DOCKER_REGISTRY = '288761770474.dkr.ecr.us-east-1.amazonaws.com'
        APP_NAME = 'flask-app'
    }
    stages {
        stage('Build') {
            steps {
                sh 'docker build -t $APP_NAME .'
            }
        }
        stage('Push') {
            steps {
                sh '''
                docker tag $APP_NAME:latest $DOCKER_REGISTRY/$APP_NAME:latest
                docker push $DOCKER_REGISTRY/$APP_NAME:latest
                '''
            }
        }
        stage('Deploy') {
            steps {
                sh 'kubectl apply -f k8s/deployment.yaml'
                sh 'kubectl apply -f k8s/service.yaml'
            }
        }
    }
}

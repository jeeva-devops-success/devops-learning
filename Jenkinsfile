pipeline {
  agent any
  environment {
    IMAGE_NAME = 'jeevan073/devops-demo'
    IMAGE_TAG  = "${BUILD_NUMBER}"
    FULL_IMAGE = "${IMAGE_NAME}:${IMAGE_TAG}"
  }
  stages {
    stage('Checkout') {
      steps { git url: 'https://github.com/jeeva-devops-success/devops-learning.git' }
    }
    stage('Start Minikube') {
      steps {
        sh '''
          minikube start
          eval $(minikube docker-env)
        '''
      }
    }
    stage('Build Docker Image') {
      steps { sh 'docker build -t $FULL_IMAGE .' }
    }
    stage('Deploy to Kubernetes') {
      steps {
        sh '''
          kubectl set image deployment/devops-demo \
            devops-container=$FULL_IMAGE \
            --namespace=default
          kubectl rollout status deployment/devops-demo \
            --namespace=default
        '''
      }
    }
  }
  post {
    success { echo "✅ Deployed $FULL_IMAGE successfully!" }
    failure { echo "🚨 Pipeline failed!" }
  }
}

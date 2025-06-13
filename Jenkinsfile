pipeline {
  agent any
  environment {
    IMAGE_NAME = 'jeevan073/devops-demo'
    IMAGE_TAG  = "${BUILD_NUMBER}"
    FULL_IMAGE = "${IMAGE_NAME}:${IMAGE_TAG}"
  }
  stages {
    stage('Checkout') {
      steps { git 'https://github.com/jeeva-devops-success/devops-learning.git' }
    }
    stage('Build') {
      steps { sh 'docker build -t $FULL_IMAGE .' }
    }
    stage('Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable:'DOCKER_USER', passwordVariable:'DOCKER_PASS')]) {
          sh '''
            echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
            docker push $FULL_IMAGE
          '''
        }
      }
    }
    stage('Deploy to k8s') {
      steps {
        withCredentials([file(credentialsId: 'kubeconfig-file', variable: 'KUBECONFIG')]) {
          sh '''
            export KUBECONFIG=$KUBECONFIG
            kubectl apply -f deployment.yaml
            kubectl rollout status deployment/devops-demo
          '''
        }
      }
    }
  }
  post {
    success { echo "✅ Deployed $FULL_IMAGE" }
    failure { echo "🚨 Pipeline failed" }
  }
}

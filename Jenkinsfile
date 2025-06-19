pipeline {
  agent any
  environment {
    IMAGE_NAME = 'jeevan073/devops-demo'
    IMAGE_TAG  = "${BUILD_NUMBER}"
    FULL_IMAGE = "${IMAGE_NAME}:${IMAGE_TAG}"
  }
  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/jeeva-devops-success/devops-learning.git'
      }
    }
    stage('Build Docker Image') {
      steps {
        sh 'docker build -t $FULL_IMAGE .'
      }
    }
    stage('Push to Docker Hub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable:'DOCKER_USER', passwordVariable:'DOCKER_PASS')]) {
          sh '''
            echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
            docker push $FULL_IMAGE
          '''
        }
      }
    }
    stage('Deploy to Kubernetes') {
      steps {
        withKubeConfig(credentialsId: 'kubeconfig-file') {
          sh '''
            kubectl set image deployment/devops-demo devops-container=$FULL_IMAGE --namespace=default
            kubectl rollout status deployment/devops-demo --namespace=default
          '''
        }
      }
    }
  }
  post {
    success { echo "✅ Successfully deployed ${FULL_IMAGE} to Kubernetes" }
    failure { echo "🚨 Pipeline failed" }
  }
}

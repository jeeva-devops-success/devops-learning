pipeline {
  agent any
  environment {
    IMAGE_NAME = 'jeevan073/devops-demo'
    IMAGE_TAG  = "${BUILD_NUMBER}"
    FULL_IMAGE = "${IMAGE_NAME}:${IMAGE_TAG}"
  }
  stages {
    stage('Checkout') {
      steps { git url: 'https://github.com/jeeva-devops-success/devops-learning.git', branch: 'main' }
    }
    stage('Build') {
      steps {
        sh 'docker build -t $FULL_IMAGE .'
      }
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
        sh '''
          kubectl set image deployment/devops-demo devops-container=$FULL_IMAGE
          kubectl rollout status deployment/devops-demo
        '''
      }
    }
  }
  post {
    success { echo "✅ Deployed ${FULL_IMAGE} to Kubernetes" }
    failure { echo "🚨 Pipeline failed" }
  }
}

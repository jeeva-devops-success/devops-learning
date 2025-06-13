pipeline {
  agent any
  environment {
    IMAGE_NAME = 'jeevan073/devops-demo'
    IMAGE_TAG  = "${BUILD_NUMBER}"
    FULL_IMAGE = "${IMAGE_NAME}:${IMAGE_TAG}"
    KUBE_CONFIG = credentials('kubeconfig-file')
  }
  stages {
    stage('Checkout') {
      steps {
        git url: 'https://github.com/jeeva-devops-success/devops-learning.git', branch: 'main'
      }
    }
    stage('Build Docker Image') {
      steps {
        sh "docker build -t $FULL_IMAGE ."
      }
    }
    stage('Push to Docker Hub') {
      steps {
        withCredentials([usernamePassword(credentialsId:'dockerhub-creds', usernameVariable:'DOCKER_USER', passwordVariable:'DOCKER_PASS')]) {
          sh '''
            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
            docker push $FULL_IMAGE
          '''
        }
      }
    }
    stage('Deploy to Kubernetes') {
      steps {
        withCredentials([file(credentialsId:'kubeconfig-file', variable:'KUBECONFIG')]) {
          // Point kubectl to the secret kubeconfig file
          sh 'export KUBECONFIG=$KUBECONFIG'
          sh '''
            kubectl set image deployment/devops-demo devops-container=$FULL_IMAGE --namespace=default || true
            kubectl apply -f deployment.yaml -n default
            kubectl apply -f service.yaml -n default
            kubectl rollout status deployment/devops-demo -n default
          '''
        }
      }
    }
  }
  post {
    success { echo "✅ Successfully built, pushed, and deployed $FULL_IMAGE" }
    failure { echo "🚨 Pipeline failed! Check the console output." }
  }
}

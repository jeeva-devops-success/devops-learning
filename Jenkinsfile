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
        git url: 'https://github.com/jeeva-devops-success/devops-learning.git', branch: 'main'
      }
    }
    stage('Build Docker Image') {
      steps {
        sh 'eval $(minikube docker-env) && docker build -t $FULL_IMAGE .'
      }
    }
    stage('Push to Docker Hub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          sh '''
            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
            docker push "$FULL_IMAGE"
          '''
        }
      }
    }
    stage('Deploy to Kubernetes') {
      steps {
        withCredentials([file(credentialsId: 'kube-sa-token', variable: 'KUBE_TOKEN_FILE')]) {
          script {
            def KUBE_API = sh(returnStdout: true, script: "kubectl config view --minify -o jsonpath='{.clusters[0].cluster.server}'").trim()
            sh """
              kubectl --server=${KUBE_API} --token="\$(cat $KUBE_TOKEN_FILE)" apply -f deployment.yaml
              kubectl --server=${KUBE_API} --token="\$(cat $KUBE_TOKEN_FILE)" apply -f service.yaml
              kubectl --server=${KUBE_API} --token="\$(cat $KUBE_TOKEN_FILE)" rollout status deployment/devops-demo
            """
          }
        }
      }
    }
  }
  post {
    success { echo "✅ Completed: $FULL_IMAGE deployed to Kubernetes" }
    failure { echo "🚨 Pipeline failed — check logs" }
  }
}

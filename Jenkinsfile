pipeline {
  agent any
  environment {
    IMAGE_NAME = 'jeevan073/devops-demo'
    IMAGE_TAG = "${BUILD_NUMBER}"
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
        sh 'docker build -t $FULL_IMAGE .'
      }
    }

    stage('Push to Docker Hub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                                         usernameVariable: 'DOCKER_USER',
                                         passwordVariable: 'DOCKER_PASS')]) {
          sh '''
            echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
            docker push $FULL_IMAGE
          '''
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        withCredentials([file(credentialsId: 'kubeconfig-file', variable: 'KUBECONFIG')]) {
          sh '''
            kubectl --kubeconfig=$KUBECONFIG apply -f deployment.yaml
            kubectl --kubeconfig=$KUBECONFIG apply -f service.yaml
            kubectl --kubeconfig=$KUBECONFIG set image deployment/devops-demo devops-container=$FULL_IMAGE
            kubectl --kubeconfig=$KUBECONFIG rollout status deployment/devops-demo
          '''
        }
      }
    }
  }

  post {
    success {
      echo "✅ Successfully built, pushed, and deployed ${FULL_IMAGE}!"
    }
    failure {
      echo "🚨 Pipeline failed — check the logs."
    }
  }
}

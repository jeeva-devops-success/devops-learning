pipeline {
  agent any

  environment {
    IMAGE_NAME = "jeevan073/devops-demo"
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
        script {
          docker.build(FULL_IMAGE)
        }
      }
    }

    stage('Push to Docker Hub') {
      steps {
        script {
          docker.withRegistry('', 'dockerhub-creds') {
            docker.image(FULL_IMAGE).push()
          }
        }
      }
    }
    
     stage('Deploy to Kubernetes') {
      steps {
        script {
          sh '''
            kubectl apply -f deployment.yaml
            kubectl apply -f service.yaml
          '''
        }
      }
    }
  }

  post {
    failure { echo "🚨 Pipeline failed—check console!" }
    success { echo "✅ Image pushed and deployment updated: ${FULL_IMAGE}" }
  }
}

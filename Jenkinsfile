pipeline {
  agent any

  environment {
    IMAGE_NAME = "jeeva/devops-demo"
    IMAGE_TAG  = "${BUILD_NUMBER}"
    FULL_IMAGE = "${IMAGE_NAME}:${IMAGE_TAG}"
  }

  stages {
    stage('Checkout Code') {
      steps {
        git url: 'https://github.com/jeeva-devops-success/devops-learning.git', branch: 'main'
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          docker.build(FULL_IMAGE)
        }
  stages {
    stage('Build Docker Image') {
      steps {
        sh 'docker build -t jeeva/devops-demo:latest .'
      }
    }

    stage('Push to Docker Hub') {
      steps {
        script {
          docker.withRegistry('', 'dockerhub-creds') {
            docker.image(FULL_IMAGE).push()
          }
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          sh '''
            echo "" | docker login -u "" --password-stdin
            docker push jeeva/devops-demo:latest
          '''
        }
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        script {
          sh """
            kubectl set image deployment/devops-demo \
              devops-container=${FULL_IMAGE} \
              --namespace=default
            kubectl rollout status deployment/devops-demo \
              --namespace=default
          """
        }
      }
    }
  }

  post {
    failure { echo "🚨 Pipeline failed—check the console output!" }
    success { echo "✅ Docker image pushed and deployed: ${FULL_IMAGE}" }
  }
}

        sh '''
          kubectl apply -f deployment.yaml
          kubectl apply -f service.yaml
        '''
      }
    }
  }
}

pipeline {
  agent any

  environment {
    IMAGE = "simple-webpage:v1"
    NAMESPACE = "web"
  }

  stages {
    stage('Build Docker Image') {
      steps {
        sh 'docker build -t $IMAGE .'
      }
    }

    stage('Push Image (optional)') {
      when {
        expression { return false }  // Skip if using local Docker socket
      }
      steps {
        echo "Push skipped due to local Docker usage"
      }
    }

    stage('Deploy to K8s') {
      steps {
        sh '''
          kubectl create ns $NAMESPACE --dry-run=client -o yaml | kubectl apply -f -
          kubectl apply -f k8s.yaml -n $NAMESPACE
        '''
      }
    }
  }

  post {
    success {
      echo "✅ Web page deployed!"
    }
    failure {
      echo "❌ Deployment failed."
    }
  }
}

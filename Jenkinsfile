pipeline {
  agent any

  environment {
    IMAGE = "simple-webpage:v1"
    NAMESPACE = "web"
  }

  stages {
    stage('Build Docker Image') {
      steps {
        script {
          sh "docker build -t ${env.IMAGE} ."
        }
      }
    }

    stage('Push Image (optional)') {
      when {
        expression { return false } // Set to true if you want to push to a registry
      }
      steps {
        echo "Push skipped due to local Docker usage"
      }
    }

    stage('Deploy to K8s') {
  steps {
    script {
      sh '''
        curl -LO "https://dl.k8s.io/release/$(curl -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
        chmod +x kubectl && mv kubectl /usr/local/bin/

        kubectl version --client
        kubectl create ns ${NAMESPACE} --dry-run=client -o yaml | kubectl apply -f -
        kubectl apply -f k8s.yaml -n ${NAMESPACE}
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

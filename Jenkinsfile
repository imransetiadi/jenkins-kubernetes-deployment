pipeline {
  environment {
    dockerimagename = "imransetiadi22/react-app"
    dockerImage = ""
    KUBECONFIG_CREDENTIALS_ID = 'kubeconfig'
  }
  agent any
  stages {
    stage('Checkout Source') {
      steps {
        git branch: 'main', url: 'https://github.com/imransetiadi/jenkins-kubernetes-deployment.git'
      }
    }
    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build dockerimagename
        }
      }
    }
    stage('Pushing Image') {
      environment {
          registryCredential = 'dockerhub-credentials'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push("latest")
          }
        }
      }
    }
    stage('Deploying React.js container to Kubernetes') {
      steps {
          withKubeConfig([credentialsId: "${KUBECONFIG_CREDENTIALS_ID}"]) {
              sh 'curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"'
              sh 'chmod +x ./kubectl'
              sh 'sudo mv ./kubectl /usr/local/bin/kubectl'
          }
      }
    }
    stage('Deploy Service YAML') {
      steps {
          withKubeConfig([credentialsId: "${KUBECONFIG_CREDENTIALS_ID}"]) {
              sh """
              kubectl apply -f service.yaml
              """
          }
      }
    }
  }
}

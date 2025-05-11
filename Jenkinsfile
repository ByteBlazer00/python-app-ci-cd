pipeline {
  agent any
  stages {
    stage('Checkout Code') {
      steps {
        git(credentialsId: env.GITHUB_CREDENTIALS_ID, url: 'https://github.com/ByteBlazer00/python-app-ci-cd.git', branch: 'main')
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          dockerImage = docker.build("${DOCKERHUB_USERNAME}/${DOCKERHUB_REPOSITORY}:${IMAGE_TAG}")
        }

      }
    }

    stage('Push to DockerHub') {
      steps {
        script {
          docker.withRegistry('https://index.docker.io/v1/', env.DOCKERHUB_CREDENTIALS_ID) {
            dockerImage.push()
            dockerImage.push('latest')
          }
        }

      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        withKubeConfig(credentialsId: env.KUBECONFIG_CREDENTIALS_ID) {
          script {
            sh """
            kubectl get ns ${K8S_NAMESPACE} || kubectl create ns ${K8S_NAMESPACE}
            kubectl apply -n ${K8S_NAMESPACE} -f k8s/deployment.yaml
            kubectl set image deployment/python-app python-app=docker.io/${DOCKERHUB_USERNAME}/${DOCKERHUB_REPOSITORY}:${IMAGE_TAG} -n ${K8S_NAMESPACE}
            """
          }

        }

      }
    }

    stage('Verify Deployment') {
      steps {
        withKubeConfig(credentialsId: env.KUBECONFIG_CREDENTIALS_ID) {
          sh "kubectl rollout status deployment/python-app -n ${K8S_NAMESPACE}"
        }

      }
    }

  }
  environment {
    DOCKERHUB_USERNAME = 'rishi8669'
    DOCKERHUB_REPOSITORY = 'python-app'
    IMAGE_TAG = "${BUILD_NUMBER}"
    DOCKERHUB_CREDENTIALS_ID = 'dockerhub-credentials'
    GITHUB_CREDENTIALS_ID = 'github-token'
    KUBECONFIG_CREDENTIALS_ID = 'kubeconfig'
    K8S_NAMESPACE = 'python-app'
  }
}
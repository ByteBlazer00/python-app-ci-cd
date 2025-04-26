pipeline {
    agent any
    environment {
        DOCKERHUB_REGISTRY = 'docker.io/rishi8669'
        DOCKERHUB_REPOSITORY = 'python-app'
        IMAGE_TAG = "${env.BUILD_NUMBER}"
        DOCKERHUB_CREDENTIALS_ID = 'dockerhub-credentials'
        GITHUB_CREDENTIALS_ID = 'jenkins'
        KUBECONFIG_CREDENTIALS_ID = 'kubeconfig'
    }
    stages {
        stage('Checkout') {
            steps {
                git credentialsId: env.GITHUB_CREDENTIALS_ID, url: 'https://github.com/ByteBlazer00/python-app-ci-cd.git', branch: 'main'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKERHUB_REGISTRY}/${DOCKERHUB_REPOSITORY}:${IMAGE_TAG}")
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', env.DOCKERHUB_CREDENTIALS_ID) {
                        docker.image("${DOCKERHUB_REGISTRY}/${DOCKERHUB_REPOSITORY}:${IMAGE_TAG}").push()
                        docker.image("${DOCKERHUB_REGISTRY}/${DOCKERHUB_REPOSITORY}:${IMAGE_TAG}").push('latest')
                    }
                }
            }
        }
        stage('Deploy to k3s') {
            steps {
                withKubeConfig([credentialsId: env.KUBECONFIG_CREDENTIALS_ID]) {
                    sh 'kubectl apply -f k8s/deployment.yaml'
                    sh "kubectl set image deployment/python-app python-app=${DOCKERHUB_REGISTRY}/${DOCKERHUB_REPOSITORY}:${IMAGE_TAG} -n python-app"
                }
            }
        }
        stage('Verify Deployment') {
            steps {
                withKubeConfig([credentialsId: env.KUBECONFIG_CREDENTIALS_ID]) {
                    sh 'kubectl rollout status deployment/python-app -n python-app'
                }
            }
        }
    }
}

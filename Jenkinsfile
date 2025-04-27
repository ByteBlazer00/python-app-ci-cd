pipeline {
    agent any
    environment {
        DOCKERHUB_USERNAME = 'rishi8669'                // Your DockerHub username
        DOCKERHUB_REPOSITORY = 'python-app'              // Your repo name
        IMAGE_TAG = "${env.BUILD_NUMBER}"                // Jenkins Build Number as Docker tag
        DOCKERHUB_CREDENTIALS_ID = 'dockerhub-credentials'
        GITHUB_CREDENTIALS_ID = 'github-token'
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
                    docker.build("${DOCKERHUB_USERNAME}/${DOCKERHUB_REPOSITORY}:${IMAGE_TAG}")
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', env.DOCKERHUB_CREDENTIALS_ID) {
                        docker.image("${DOCKERHUB_USERNAME}/${DOCKERHUB_REPOSITORY}:${IMAGE_TAG}").push()
                        docker.image("${DOCKERHUB_USERNAME}/${DOCKERHUB_REPOSITORY}:${IMAGE_TAG}").push('latest')
                    }
                }
            }
        }

        stage('Deploy to k3s') {
            steps {
                withKubeConfig([credentialsId: env.KUBECONFIG_CREDENTIALS_ID]) {
                    script {
                        sh '''
                        # Create namespace if not exists
                        kubectl get namespace python-app || kubectl create namespace python-app

                        # Replace image tag dynamically in deployment YAML if needed
                        kubectl set image deployment/python-app python-app=${DOCKERHUB_USERNAME}/${DOCKERHUB_REPOSITORY}:${IMAGE_TAG} -n python-app || kubectl apply -f k8s-deployment.yaml
                        '''
                    }
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

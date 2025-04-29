pipeline {
    agent any
    environment {
        DOCKERHUB_USERNAME = 'rishi8669'
        DOCKERHUB_REPOSITORY = 'python-app'
        IMAGE_TAG = "${env.BUILD_NUMBER}"
        DOCKERHUB_CREDENTIALS_ID = 'dockerhub-credentials'
        GITHUB_CREDENTIALS_ID = 'github-token'
        KUBECONFIG_CREDENTIALS_ID = 'kubeconfig'
    }
    stages {
        stage('Checkout Code') {
            steps {
                git credentialsId: env.GITHUB_CREDENTIALS_ID,
                    url: 'https://github.com/ByteBlazer00/python-app-ci-cd.git',
                    branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKERHUB_USERNAME}/${DOCKERHUB_REPOSITORY}:${IMAGE_TAG}")
                }
            }
        }

        stage('Push to DockerHub') {
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
                        kubectl get namespace python-app >/dev/null 2>&1 || kubectl create namespace python-app
                        kubectl apply -f k8s/deployment.yaml
                        kubectl set image deployment/python-app python-app=docker.io/${DOCKERHUB_USERNAME}/${DOCKERHUB_REPOSITORY}:${IMAGE_TAG} -n python-app
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

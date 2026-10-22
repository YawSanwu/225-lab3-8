pipeline {
    agent any

    environment {
        GITHUB_URL = 'https://github.com/YawSanwu/225-lab3-8.git'
        KUBECONFIG = credentials('sanwuya-225')
    }

    stages {
        stage('Checkout') {
            steps {
                cleanWs()
                git branch: 'main', url: "${GITHUB_URL}"
            }
        }

        stage('Deploy MongoDB Configuration') {
            steps {
                sh '''
                    kubectl apply -f mongo-secret.yaml
                    kubectl apply -f mongo-configmap.yaml
                '''
            }
        }

        stage('Deploy MongoDB') {
            steps {
                sh 'kubectl apply -f mongo.yaml'
            }
        }

        stage('Wait for MongoDB') {
            steps {
                sh 'kubectl rollout status statefulset/mongo --timeout=300s'
            }
        }

        stage('Deploy Mongo Express') {
            steps {
                sh 'kubectl apply -f mongo-express.yaml'
            }
        }

        stage('Wait for Mongo Express') {
            steps {
                sh 'kubectl rollout status deployment/mongo-express --timeout=300s'
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                    kubectl get pods
                    kubectl get services
                    kubectl get statefulsets
                    kubectl get deployments
                '''
            }
        }
    }

    post {
        success {
            echo 'Lab 3.8 deployment successful!'
        }

        failure {
            echo 'Lab 3.8 failed. Check Console Output.'
        }
    }
}

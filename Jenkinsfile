
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
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        url: "${GITHUB_URL}"
                    ]]
                ])
            }
        }

        stage('Deploy MongoDB') {
            steps {
                sh 'kubectl apply -f mongo-configmap.yaml'
                sh 'kubectl apply -f mongo.yaml'
            }
        }

        stage('Wait for MongoDB') {
            steps {
                sh '''
                    kubectl rollout status statefulset/mongo \
                    --timeout=180s
                '''
            }
        }

        stage('Deploy Mongo Express') {
            steps {
                sh 'kubectl apply -f mongo-express.yaml'
            }
        }

        stage('Wait for Mongo Express') {
            steps {
                sh '''
                    kubectl rollout status deployment/mongo-express \
                    --timeout=180s
                '''
            }
        }

        stage('Check Kubernetes Cluster') {
            steps {
                sh 'kubectl get pods'
                sh 'kubectl get services'
                sh 'kubectl get statefulsets'
                sh 'kubectl get deployments'
            }
        }
    }

    post {
        success {
            echo "Lab 3.8 pipeline completed successfully."
        }

        failure {
            echo "Lab 3.8 pipeline failed. Check Console Output."
        }
    }
}

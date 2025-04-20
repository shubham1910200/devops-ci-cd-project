pipeline {
    agent any

    environment {
        IMAGE_NAME = 'soma1999/ci-cd-app:latest'
        KUBECONFIG = '/var/lib/jenkins/.kube/config'
    }

    stages {
        stage('Clone') {
            steps {
                echo 'Cloning the GitHub repository...'
                git branch: 'main', url: 'https://github.com/shubham1910200/devops-ci-cd-project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image: $IMAGE_NAME"
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo 'Pushing Docker image to Docker Hub...'
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push $IMAGE_NAME
                        docker logout
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo 'Deploying to Kubernetes cluster...'
                withCredentials([file(credentialsId: 'kubeconfig-creds', variable: 'KUBECONFIG')]) {
                    
                
                sh 'kubectl apply -f kubernetes/deployment.yaml --insecure-skip-tls-verify'
                sh 'kubectl apply -f kubernetes/service.yaml'

                }
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline executed successfully!'
        }
        failure {
            echo '❌ Pipeline failed. Check the logs for more information.'
        }
        always {
            echo '🧹 Cleanup or final steps can go here.'
        }
    }
}

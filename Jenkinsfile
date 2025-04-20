pipeline {
    agent any

    environment {
        IMAGE_NAME = 'soma1999/ci-cd-app:latest'
    }

    stages {
        stage('Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/shubham1910200/devops-ci-cd-project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push $IMAGE_NAME
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
           
           steps {
            script {
                kubernetesDeploy(configs:"kubernetes/deployment.yaml", kubeconfigId: 'kubernetes')
                kubernetesDeploy(configs:"kubernetes/service.yaml", kubeconfigId: 'kubernetes')
            }
           }
        }
    }
}

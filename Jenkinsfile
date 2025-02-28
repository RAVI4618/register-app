pipeline {
    agent any

    tools {
        jdk 'jdk'
        maven 'maven3'
    }

    environment {
        DOCKER_HUB_CREDENTIALS = 'docker-cred'  // Jenkins credentials ID for Docker Hub
        DOCKER_IMAGE = 'ravi011/register-app'   // Docker Hub username and repository
        KUBE_CONFIG = '/var/lib/jenkins/.kube/config'  // Kubeconfig path
    }

    stages {
        stage('SCM') {
            steps {
                git branch: 'main', credentialsId: 'GITHUB', url: 'https://github.com/RAVI4618/register-app.git'
            }
        }

        stage('Build Application') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Docker Build') {
            steps {
                script {
                    sh 'docker build -t ${env.DOCKER_IMAGE}:latest .'
                }
            }
        }

        stage('Docker Login') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: env.DOCKER_HUB_CREDENTIALS, usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh "echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin"
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    sh 'docker push ${env.DOCKER_IMAGE}:latest'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh 'kubectl --kubeconfig=${env.KUBE_CONFIG} apply -f /var/lib/jenkins/deploy.yaml'
                }
            }
        }
    }
}

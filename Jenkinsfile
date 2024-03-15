pipeline {
    agent any

    environment {
        // Define environment variables here
        DOCKER_IMAGE = 'gym14714/carti'  // Replace with your image name
        DOCKER_REGISTRY = 'gym14714'  // Replace with your Docker registry URL
        // Assuming you have added your kubeconfig as a file credential
        KUBECONFIG_CREDENTIAL_ID = 'my-kubeconfig-file'
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Checks out the code from the repository
                checkout scm
            }
        }

        stage('Maven Package') {
            steps {
                // Build the project using Maven
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    sh "docker build -t ${DOCKER_REGISTRY}/${DOCKER_IMAGE} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Push the Docker image to the Docker registry
                    sh "docker push ${DOCKER_REGISTRY}/${DOCKER_IMAGE}"
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Use the kubeconfig file
                    withCredentials([file(credentialsId: KUBECONFIG_CREDENTIAL_ID, variable: 'KUBECONFIG')]) {
                        // Deploy to Kubernetes using kubectl commands
                        sh 'kubectl apply -f regapp-deploy.yml'
                    }
                }
            }
        }
    }

    post {
        always {
            // Clean-up actions, like deleting temporary files, if any
        }
        success {
            // Actions to take on success
        }
        failure {
            // Actions to take on failure
        }
    }
}
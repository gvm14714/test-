pipeline {
    agent any

    environment {
        // Define environment variables here
        DOCKER_IMAGE = 'gym14714/carti'  // Replace with your image name
        DOCKER_REGISTRY = 'gym14714'  // Replace with your Docker registry URL
        // Assuming you have added your kubeconfig as a file credential
        KUBECONFIG_CREDENTIAL_ID = 'my-kubeconfig-file'
        DOCKER_COMMAND = '/usr/local/bin/docker' // Path to the Docker executable
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
                script {
                    // Using Maven Docker image to run Maven commands
                    docker.image('maven:3.8.4-openjdk-11').inside {
                        sh 'mvn clean package'
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    sh "${DOCKER_COMMAND} build -t ${DOCKER_REGISTRY}/${DOCKER_IMAGE} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Push the Docker image to the Docker registry
                    sh "${DOCKER_COMMAND} push ${DOCKER_REGISTRY}/${DOCKER_IMAGE}"
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
}

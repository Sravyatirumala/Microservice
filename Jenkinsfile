pipeline {
    agent any

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()  // Clean the workspace before starting the pipeline
            }
        }

        stage('Build & Tag Docker Image') {
            steps {
                script {
                    // Navigate to the source directory and build the Docker image
                    dir('src') {
                        withDockerRegistry(credentialsId: 'Docker-creds', toolName: 'docker') {
                            sh "docker build -t sravyatirumala/cartservice:latest ."
                        }
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Push the built Docker image to the registry
                    withDockerRegistry(credentialsId: 'Docker-creds', toolName: 'docker') {
                        sh "docker push sravyatirumala/cartservice:latest"
                    }
                }
            }
        }
    }

    post {
        always {
            // Cleanup actions, such as removing temporary files or notifying users
            echo 'Pipeline finished, cleaning up workspace.'
            cleanWs()  // Clean the workspace after the pipeline has finished
        }
        success {
            echo 'Build and Push completed successfully!'  // Message on success
        }
        failure {
            echo 'Build or Push failed, please check the logs!'  // Message on failure
        }
    }
}

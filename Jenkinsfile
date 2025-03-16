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
            cleanWs()  // Optional: you can clean the workspace again if necessary
        }
        success {
            echo 'Build and Push completed successfully!'
        }
        failure {
            echo 'Build or Push failed, please check the logs!'
        }
    }
}

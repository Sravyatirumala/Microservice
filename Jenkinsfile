pipeline {
    agent any

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        DOCKER_IMAGE = 'sravyatirumala/adservice:latest'
        EKS_CLUSTER_NAME = 'my-eks-cluster'
        AWS_REGION = 'us-east-2'
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''
                    $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Microservice \
                    -Dsonar.projectKey=Microservice
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token'
                }
            }
        }

        stage('Build & Tag Docker Image') {
            steps {
                script {
                    // Debugging: Check the current directory and list files
                    sh 'pwd'  // Prints the current directory
                    sh 'ls -l' // Lists all files to check if Dockerfile exists
                    
                    // Check for Dockerfile existence before building
                    def dockerFileExists = fileExists('Dockerfile')
                    if (dockerFileExists) {
                        // Build the Docker image if Dockerfile exists
                        withDockerRegistry(credentialsId: 'Docker-creds', toolName: 'docker') {
                            sh "docker build --no-cache -t sravyatirumala/adservice:latest ."
                        }
                    } else {
                        error "Dockerfile not found in the workspace!"
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'Docker-creds', toolName: 'docker') {
                        sh "docker push sravyatirumala/adservice:latest"
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

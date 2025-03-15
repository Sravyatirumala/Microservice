pipeline {
    agent any

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        DOCKER_IMAGE = 'sravyatirumala/loadgenerator:latest'
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
                    $SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.projectName=Microservice \
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

        stage('Trivy FS Scan') {
            steps {
                sh 'trivy fs . > trivyfs.txt'
            }
        }

        stage('Build & Tag Docker Image') {
            steps {
                script {
                    // Debugging steps
                    sh 'pwd'  // Print current directory
                    sh 'ls -l' // List files to verify directory structure

                    // Build Docker image
                    withDockerRegistry(credentialsId: 'Docker-creds', toolName: 'docker') {
                        sh "docker build --no-cache -t sravyatirumala/loadgenerator:latest -f ./docker/Dockerfile ."
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'Docker-creds', toolName: 'docker') {
                        sh "docker push sravyatirumala/loadgenerator:latest"
                    }
                }
            }
        }
    }

    post {
        always {
            // Cleanup actions
            echo 'Pipeline finished, cleaning up workspace.'
            cleanWs()
        }
        success {
            echo 'Build and Push completed successfully!'
        }
        failure {
            echo 'Build or Push failed, please check the logs!'
        }
    }
}

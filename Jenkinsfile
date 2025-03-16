pipeline {
    agent any

    environment {
        SCANNER_HOME = tool 'sonar-scanner'  // Set up the SonarQube scanner tool path
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()  // Clean the workspace before starting the pipeline
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {  // Set up the SonarQube environment variables
                    sh ''' 
                    $SCANNER_HOME/bin/sonar-scanner \  // Use the SonarQube scanner
                        -Dsonar.projectName=Microservice \
                        -Dsonar.projectKey=Microservice
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token'  // Wait for the SonarQube quality gate
                }
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

        stage('Trivy FS Scan') {
            steps {
                sh 'trivy fs . > trivyfs.txt'
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

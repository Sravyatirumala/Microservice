pipeline {
    agent any
    
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        DOCKER_IMAGE = 'sravyatirumala/cartservice:latest'
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
                    $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=BMS \
                    -Dsonar.projectKey=BMS 
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
        
    stages {
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
                        sh "docker push sravyatirumala/cartservice:latest "
                    }
                }
            }
        }
    }
}

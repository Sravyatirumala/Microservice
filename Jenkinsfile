pipeline {
    agent any

    stages {
        stage('Build & Tag Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'Docker-creds', toolName: 'docker') {
                        sh "docker build -t sravyatirumala/loadgenerator:latest ."
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
}

pipeline {
    agent any

    stages {
        stage('Deploy To Kubernetes') {
            steps {
                withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'my-eks-cluster', contextName: '', credentialsId: 'k8-token', namespace: 'webapps', serverUrl: 'https://D5A7624D64C34F90DA8AE217CC814CE9.yl4.us-east-2.eks.amazonaws.com']]) {
                    sh "kubectl apply -f deployment-service.yml"
                    
                }
            }
        }
        
        stage('verify Deployment') {
            steps {
                withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'my-eks-cluster', contextName: '', credentialsId: 'k8-token', namespace: 'webapps', serverUrl: 'https://D5A7624D64C34F90DA8AE217CC814CE9.yl4.us-east-2.eks.amazonaws.com']]) {
                    sh "kubectl get svc -n webapps"
                }
            }
        }
    }
}

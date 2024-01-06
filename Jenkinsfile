pipeline {
    agent {
        label 'kubectl' // Use an agent with the "kubectl" label
    }

    stages {
        stage('Deploy to Kubernetes') {

            steps {
                withCredentials([string(credentialsId: 'backstage-backend-secret', variable: 'BackstageBackendSecret')]) {
                    sh "sed -i 's|BACKSTAGE_BACKEND_SECRET_TEXT|${BackstageBackendSecret}|g' backstage-secrets.yaml"
                    sh "cat secret.yaml"
                }
                
                // Deploy to Kubernetes
                withKubeConfig(clusterName: 'ttf-cluster', contextName: 'jenkins-k8s', credentialsId: '1c00907c-98ab-4c55-bd44-7afc075d4ac8', namespace: '', restrictKubeConfigAccess: false, serverUrl: 'https://kubernetes.default') {
                    sh 'kubectl get ns'
                    sh 'kubectl apply -f backstage-secrets.yaml -n testsekkreit'
                }
            }
        }
    }
}

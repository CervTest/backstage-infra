pipeline {
    agent {
        label 'kubectl' 
    }

    stages {
        stage('Prepare Secrets') {

            steps {
                // Grab each desired credential and apply it to a local file
                withCredentials([string(credentialsId: 'backstage-backend-secret', variable: 'backstageBackendSecret')]) {
                    sh "sed -i 's|BACKSTAGE_BACKEND_SECRET_TEXT|${backstageBackendSecret}|g' backstage-secrets.yaml"
                }

                withCredentials([string(credentialsId: 'backstage-postgres-password', variable: 'backstagePostgresPassword')]) {
                    sh "sed -i 's|BACKSTAGE_POSTGRES_PASSWORD|${backstagePostgresPassword}|g' backstage-secrets.yaml"
                }

                withCredentials([string(credentialsId: 'backstage-github-token', variable: 'backstageGitHubToken')]) {
                    sh "sed -i 's|BACKSTAGE_GITHUB_TOKEN|${backstageGitHubToken}|g' backstage-secrets.yaml"
                }

                withCredentials([string(credentialsId: 'backstage-github-auth-client-id', variable: 'backstageGitHubAuthClientId')]) {
                    sh "sed -i 's|BACKSTAGE_GITHUB_AUTH_CLIENT_ID|${backstageGitHubAuthClientId}|g' backstage-secrets.yaml"
                }

                withCredentials([string(credentialsId: 'backstage-github-auth-client-secret', variable: 'backstageGitHubAuthClientSecret')]) {
                    sh "sed -i 's|BACKSTAGE_GITHUB_AUTH_CLIENT_SECRET|${backstageGitHubAuthClientSecret}|g' backstage-secrets.yaml"
                }

                withCredentials([string(credentialsId: 'premium-backstage-plugins-spotify-license', variable: 'premiumBackstagePluginsSpotifyLicense')]) {
                    sh "sed -i 's|PREMIUM_BACKSTAGE_PLUGINS_SPOTIFY_LICENSE|${premiumBackstagePluginsSpotifyLicense}|g' backstage-secrets.yaml"
                }
                
                // Deploy the secret to Kubernetes
                withKubeConfig(clusterName: 'ttf-cluster', contextName: 'jenkins-k8s', credentialsId: '1c00907c-98ab-4c55-bd44-7afc075d4ac8', namespace: '', restrictKubeConfigAccess: false, serverUrl: 'https://kubernetes.default') {
                    sh 'kubectl apply -f backstage-secrets.yaml -n testsekkreit'
                }
            }
        }

        stage('Apply via Helm') {
            steps {
                withKubeConfig(clusterName: 'ttf-cluster', contextName: 'jenkins-k8s', credentialsId: '1c00907c-98ab-4c55-bd44-7afc075d4ac8', namespace: '', restrictKubeConfigAccess: false, serverUrl: 'https://kubernetes.default') {
                    sh 'helm dependency build'
                    sh 'helm upgrade -f values.yaml backstage .'
                }
            }
        }
    }
}

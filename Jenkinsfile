pipeline {
    agent {
        label 'kubectl' 
    }

    environment {
        BACKSTAGE_BACKEND_SECRET = credentials('backstage-backend-secret')
        BACKSTAGE_POSTGRES_PASSWORD = credentials('backstage-postgres-password')
        BACKSTAGE_GITHUB_TOKEN = credentials('backstage-github-token')
        BACKSTAGE_GITHUB_AUTH_CLIENT_ID = credentials('backstage-github-auth-client-id')
        BACKSTAGE_GITHUB_AUTH_CLIENT_SECRET = credentials('backstage-github-auth-client-secret')
        PREMIUM_BACKSTAGE_PLUGINS_SPOTIFY_LICENSE = credentials('premium-backstage-plugins-spotify-license')
        JENKINS_API_TOKEN_CERVATOR = credentials('jenkins-api-token-cervator')
        SONAR_TOKEN_ADMIN_USER = credentials('sonar-api-token-admin-user')
        NEXUS_USER_PASS_ENCODED = credentials('nexus-cred-base64')
        AVST_GITLAB_TOKEN = credentials('avst_gitlab_token')
        JIRA_ENCODED_TOKEN = credentials('jira-token-rasmus-venue')
        CONFLUENCE_USER = credentials('confluence-user-rasmus-venue')
        CONFLUENCE_PASS = credentials('confluence-password-rasmus-venue')
    }

    stages {
        stage('Prepare Secrets') {
            steps {
                sh 'sed -i "s|BACKSTAGE_BACKEND_SECRET_TEXT|${BACKSTAGE_BACKEND_SECRET}|g" backstage-secrets.yaml'
                sh 'sed -i "s|BACKSTAGE_POSTGRES_PASSWORD|${BACKSTAGE_POSTGRES_PASSWORD}|g" backstage-secrets.yaml'
                sh 'sed -i "s|BACKSTAGE_GITHUB_TOKEN|${BACKSTAGE_GITHUB_TOKEN}|g" backstage-secrets.yaml'
                sh 'sed -i "s|BACKSTAGE_GITHUB_AUTH_CLIENT_ID|${BACKSTAGE_GITHUB_AUTH_CLIENT_ID}|g" backstage-secrets.yaml'
                sh 'sed -i "s|BACKSTAGE_GITHUB_AUTH_CLIENT_SECRET|${BACKSTAGE_GITHUB_AUTH_CLIENT_SECRET}|g" backstage-secrets.yaml'
                sh 'sed -i "s|PREMIUM_BACKSTAGE_PLUGINS_SPOTIFY_LICENSE|${PREMIUM_BACKSTAGE_PLUGINS_SPOTIFY_LICENSE}|g" backstage-secrets.yaml'
                sh 'sed -i "s|JENKINS_API_TOKEN_CERVATOR|${JENKINS_API_TOKEN_CERVATOR}|g" backstage-secrets.yaml'
                sh 'sed -i "s|SONAR_TOKEN_ADMIN_USER|${SONAR_TOKEN_ADMIN_USER}|g" backstage-secrets.yaml'
                sh 'sed -i "s|NEXUS_USER_PASS_ENCODED|${NEXUS_USER_PASS_ENCODED}|g" backstage-secrets.yaml'
                sh 'sed -i "s|AVST_GITLAB_TOKEN|${AVST_GITLAB_TOKEN}|g" backstage-secrets.yaml'
                sh 'sed -i "s|JIRA_ENCODED_TOKEN|${JIRA_ENCODED_TOKEN}|g" backstage-secrets.yaml'
                sh 'sed -i "s|CONFLUENCE_ACCOUNT_USER|${CONFLUENCE_ACCOUNT_USER}|g" backstage-secrets.yaml'
                sh 'sed -i "s|CONFLUENCE_ACCOUNT_PASS|${CONFLUENCE_ACCOUNT_PASS}|g" backstage-secrets.yaml'
                
                // Deploy the secret to Kubernetes
                withKubeConfig(clusterName: 'ttf-cluster', contextName: 'jenkins-k8s', credentialsId: '1c00907c-98ab-4c55-bd44-7afc075d4ac8', namespace: '', restrictKubeConfigAccess: false, serverUrl: 'https://kubernetes.default') {
                    sh 'kubectl apply -f backstage-secrets.yaml -n backstage'
                }
            }
        }

        stage('Apply via Helm') {
            steps {
                withKubeConfig(clusterName: 'ttf-cluster', contextName: 'jenkins-k8s', credentialsId: '1c00907c-98ab-4c55-bd44-7afc075d4ac8', namespace: '', restrictKubeConfigAccess: false, serverUrl: 'https://kubernetes.default') {
                    sh 'helm dependency build'
                    sh 'helm upgrade --recreate-pods -f values.yaml -n backstage backstage .'
                    // Note that without --recreate-pods the Backstage pod may not update if it is set to "latest"
                }
            }
        }
    }
}

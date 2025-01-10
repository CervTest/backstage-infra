pipeline {
    agent {
        label 'kubectl' 
    }

    environment {
        // Note: In Jenkins these may be stored under a given folder, like https://jenkins.terasology.io/job/Experimental/credentials/ rather than globally
        BACKSTAGE_BACKEND_SECRET = credentials('backstage-backend-secret')
        BACKSTAGE_POSTGRES_PASSWORD = credentials('backstage-postgres-password')
        BACKSTAGE_GITHUB_TOKEN = credentials('backstage-github-token')
        BACKSTAGE_GITHUB_AUTH_CLIENT_ID = credentials('backstage-github-auth-client-id')
        BACKSTAGE_GITHUB_AUTH_CLIENT_SECRET = credentials('backstage-github-auth-client-secret')
        PREMIUM_BACKSTAGE_PLUGINS_SPOTIFY_LICENSE = credentials('premium-backstage-plugins-spotify-license')
        //JENKINS_API_TOKEN_CERVATOR = credentials('jenkins-api-token-cervator')
        //SONAR_TOKEN_ADMIN_USER = credentials('sonar-api-token-admin-user')
        //NEXUS_USER_PASS_ENCODED = credentials('nexus-cred-base64') 
    }

    stages {
        stage('Prepare Secrets') {
            steps {
                container('utility') {
                    sh 'sed -i "s|BACKSTAGE_BACKEND_SECRET_TEXT|${BACKSTAGE_BACKEND_SECRET}|g" backstage-secrets.yaml'
                    sh 'sed -i "s|BACKSTAGE_POSTGRES_PASSWORD|${BACKSTAGE_POSTGRES_PASSWORD}|g" backstage-secrets.yaml'
                    sh 'sed -i "s|BACKSTAGE_GITHUB_TOKEN|${BACKSTAGE_GITHUB_TOKEN}|g" backstage-secrets.yaml'
                    sh 'sed -i "s|BACKSTAGE_GITHUB_AUTH_CLIENT_ID|${BACKSTAGE_GITHUB_AUTH_CLIENT_ID}|g" backstage-secrets.yaml'
                    sh 'sed -i "s|BACKSTAGE_GITHUB_AUTH_CLIENT_SECRET|${BACKSTAGE_GITHUB_AUTH_CLIENT_SECRET}|g" backstage-secrets.yaml'
                    sh 'sed -i "s|PREMIUM_BACKSTAGE_PLUGINS_SPOTIFY_LICENSE|${PREMIUM_BACKSTAGE_PLUGINS_SPOTIFY_LICENSE}|g" backstage-secrets.yaml'
                    //sh 'sed -i "s|JENKINS_API_TOKEN_CERVATOR|${JENKINS_API_TOKEN_CERVATOR}|g" backstage-secrets.yaml'
                    //sh 'sed -i "s|SONAR_TOKEN_ADMIN_USER|${SONAR_TOKEN_ADMIN_USER}|g" backstage-secrets.yaml'
                    //sh 'sed -i "s|NEXUS_USER_PASS_ENCODED|${NEXUS_USER_PASS_ENCODED}|g" backstage-secrets.yaml'
                    
                    // Deploy the secret to Kubernetes - TODO: Relies on namespace already existing
                    withKubeConfig(credentialsId: 'utility-admin-kubeconfig-sa-token') {
                        sh 'kubectl apply -f backstage-secrets.yaml -n backstage'
                    }
                }
            }
        }

        stage('Install Helm (temp)') {
            steps {
                container('utility') {
                    sh '''
                    curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
                    chmod 700 get_helm.sh
                    ./get_helm.sh
                    '''
                }
            }
        }

        stage('Apply via Helm') {
            steps {
                container('utility') {
                    withKubeConfig(credentialsId: 'utility-admin-kubeconfig-sa-token') {
                        sh 'helm dependency build'
                        // TODO: Expects the release to have already been installed, and "--recreate-pods" appears deprecated/gone
                        sh 'helm upgrade -f values.yaml -n backstage backstage .'
                        // Note that without --recreate-pods the Backstage pod may not update if it is set to "latest"
                    }
                }
            }
        }
    }
}

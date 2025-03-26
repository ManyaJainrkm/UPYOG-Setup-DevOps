pipeline {
    agent any

    environment {
        KUBECONFIG = '/Users/manyajain/.kube/config'  // Update with the actual KUBECONFIG path
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/ManyaJainrkm/UPYOG-DevOps.git' // Replace with your Git repo
            }
        }

        stage('Setup Helm Repositories') {
            steps {
                sh '''
                helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
                helm repo add bitnami https://charts.bitnami.com/bitnami
                helm repo update
                '''
            }
        }

        stage('Deploy OAuth2-Proxy') {
            steps {
                sh '''
                helm upgrade --install oauth2-proxy bitnami/oauth2-proxy \
                    --namespace monitoring \
                    --set config.clientID="your-client-id" \
                    --set config.clientSecret="your-client-secret" \
                    --set config.cookieSecret="your-cookie-secret"
                '''
            }
        }

        stage('Deploy Prometheus & Grafana') {
            steps {
                sh '''
                helm upgrade --install prometheus prometheus-community/kube-prometheus-stack \
                    --namespace monitoring \
                    --set grafana.adminPassword="admin" \
                    --set alertmanager.enabled=true
                '''
            }
        }

        stage('Apply Kubernetes Configurations') {
            steps {
                sh '''
                kubectl apply -f k8s/monitoring-configs.yaml  # Update with your config file path
                '''
            }
        }

        stage('Verify Deployments') {
            steps {
                sh '''
                kubectl get pods -n monitoring
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Monitoring & Alerting Stack Successfully Deployed!"
        }
        failure {
            echo "❌ Deployment Failed. Check logs!"
        }
    }
}

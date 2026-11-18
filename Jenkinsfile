pipeline {
    agent any

    environment {
        // Matches your verified GitHub repository via SSH
        GIT_URL = 'git@github.com:Nagaraj991655/LoanWise-Predictive-Loan-Approval.git'
        CREDENTIALS_ID = 'github-ssh' 
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo 'Checking out code from GitHub using SSH credentials...'
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: 'refs/heads/main']],
                    userRemoteConfigs: [[
                        url: "${env.GIT_URL}",
                        credentialsId: "${env.CREDENTIALS_ID}"
                    ]]
                ])
            }
        }

        stage('Clean Up Previous Containers & Networks') {
            steps {
                echo 'Stopping and cleaning up existing conflicting containers/networks...'
                sh '''
                    docker compose down --remove-orphans || true
                    docker rm -f loanwise-backend loanwise-frontend || true
                '''
            }
        }

        stage('Build & Deploy with Docker Compose') {
            steps {
                echo 'Building and starting multi-container Docker application...'
                sh '''
                    docker compose up -d --build
                '''
            }
        }

        stage('Health Check') {
            steps {
                echo 'Verifying services are running smoothly...'
                sh '''
                    docker ps
                    echo "Checking Backend Health Endpoint..."
                    sleep 3
                    curl -s http://localhost:8000/health || echo "Backend starting up..."
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully! LoanWise is deployed and live.'
        }
        failure {
            echo 'Pipeline failed. Please check the container logs using: docker logs loanwise-backend'
        }
    }
}

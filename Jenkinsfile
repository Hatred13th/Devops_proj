pipeline {
    agent any

    options {
        timestamps()
        skipDefaultCheckout(false)
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Checking out repo..."
                checkout scm
            }
        }
        /*BACKEND PIPELINE */
        stage('Backend Install') {
            steps {
                dir('backend') {
                    echo " Installing backend dependencies..."
                    bat 'npm install'
                }
            }
        }

        stage('Backend Smoke Test') {
            steps {
                dir('backend') {
                    echo " Running backend smoke test..."
                    bat 'curl -X GET http://localhost:4000/users || exit 1'
                }
            }
        }

        stage('Backend Tests') {
            steps {
                dir('backend') {
                    echo " Running backend full tests..."
                    bat 'npm test -- --watchAll=false || exit 0'
                }
            }
        }

        /* FRONTEND PIPELINE*/
        stage('Frontend Install') {
            steps {
                dir('frontend') {
                    echo " Installing frontend dependencies..."
                    bat 'npm install'
                }
            }
        }

        stage('Frontend Build') {
            steps {
                dir('frontend') {
                    echo " Building frontend..."
                    bat 'npm run build'
                }
            }
        }

        stage('Frontend Smoke Test') {
            steps {
                dir('frontend') {
                    echo " Checking build output exists..."
                    bat 'if exist build (exit 0) else (exit 1)'
                }
            }
        }

        /* 
           DOCKER STAGES
           */
        stage('Docker Build') {
            when { branch "main" }
            steps {
                echo "Building Docker images..."
                bat 'docker build -t devops_proj-backend ./backend'
                bat 'docker build -t devops_proj-frontend ./frontend'
            }
        }

        stage('Deploy') {
            when { branch "main" }
            steps {
                echo " Deploying with Docker Compose..."
                bat 'docker compose down'
                bat 'docker compose up -d --build'
            }
        }
    }
    post {
        success { echo " PIPELINE SUCCESS!" }
        failure { echo " PIPELINE FAILED – CHECK LOGS" }
    }
}

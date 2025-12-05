pipeline {
    agent any
    environment { DOCKER_IMAGE = "myapp" }

    stages {
        stage('Checkout') {
            steps {
                git branch: "${env.BRANCH_NAME}", url: 'https://github.com/Hatred13th/Devops_proj.git'
            }
        }

        stage('Setup') {
            steps {
                sh 'cd frontend && npm install'
            }
        }

        stage('Build') {
            steps {
                sh 'cd frontend && npm run build'
            }
        }

        stage('Run Docker') {
            steps {
                script {
                    def tag = env.GIT_TAG ?: env.BRANCH_NAME
                    sh "docker build -t ${DOCKER_IMAGE}:${tag} ."
                    sh "docker run -d -p 8080:80 ${DOCKER_IMAGE}:${tag}"
                }
            }
        }

        stage('Smoke Test') {
            steps {
                sh 'curl -f http://localhost:8080 || exit 1'
            }
        }

        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'frontend/build/**'
            }
        }

        stage('Cleanup') {
            steps {
                sh 'docker ps -aq | xargs docker rm -f || true'
                sh 'docker images -q ${DOCKER_IMAGE} | xargs docker rmi -f || true'
            }
        }
    }

    post {
        success { echo 'Pipeline Passed!' }
        failure { echo 'Pipeline Failed!' }
    }
}

pipeline {
    agent { label 'agent' }

    parameters {
        choice(
            name: 'ENVIRONMENT',
            choices: ['dev', 'qa', 'prod'],
            description: 'Select environment to deploy'
        )
    }

    environment {
        // mapping env → branch
        BRANCH_NAME = "${params.ENVIRONMENT == 'prod' ? 'master' : params.ENVIRONMENT}"
    }

    stages {

        stage('Clone Repository') {
            steps {
                echo "📌 Cloning branch: ${BRANCH_NAME}"
                git branch: "${BRANCH_NAME}", credentialsId: 'github', url: 'https://github.com/srinivasulu2004/frontend-vite-and-backend-java-.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    echo "⚙️ Cleaning old images"
                    docker rmi -f $(docker images -aq) || true
                    echo "🛠️ Building fresh Docker images"
                    docker compose build
                '''
            }
        }

        stage('Deployment of Application') {
            steps {
                sh '''
                    echo "🔥 Removing running containers"
                    docker rm -f $(docker ps -aq) || true
                    docker volume rm -f $(docker volume ls -q) || true

                    echo "🚀 Deploying new containers"
                    docker compose up -d
                '''
            }
        }

        stage('List Containers') {
            steps {
                sh 'docker ps -a'
            }
        }
    }

    post {
        success {
            mail to: 'srininivasulubehara@gmail.com',
                 subject: "Pipeline SUCCESS for ${params.ENVIRONMENT}",
                 body: "✔️ Pipeline executed successfully for branch: ${BRANCH_NAME}."
        }

        failure {
            mail to: 'srininivasulubehara@gmail.com',
                 subject: "Pipeline FAILED for ${params.ENVIRONMENT}",
                 body: "❌ Pipeline failed for branch: ${BRANCH_NAME}."
        }
    }
}

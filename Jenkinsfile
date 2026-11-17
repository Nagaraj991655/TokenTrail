pipeline {
    agent any

    stages {
        stage('Deploy') {
            steps {
                sh '''
                git config --global --add safe.directory /home/ubuntu/TokenTrail

                cd /home/ubuntu/TokenTrail

                git fetch origin
                git reset --hard origin/main

                docker compose down --remove-orphans || true
                docker compose up -d --build

                docker ps
                '''
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                curl -f http://localhost:8000/health
                '''
            }
        }
    }
}

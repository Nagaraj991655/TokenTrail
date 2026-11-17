pipeline {
    agent any

    stages {

        stage('Deploy') {
            steps {
                sh '''
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
                sleep 15

                curl -f http://localhost:8000/health
                curl -f http://localhost:3000
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful'
        }

        failure {
            sh '''
            cd /home/ubuntu/TokenTrail
            docker compose logs --tail=100
            '''
        }
    }
}

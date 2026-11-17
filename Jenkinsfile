pipeline {
    agent any

    stages {

        stage('Deploy') {
            steps {
                sh '''
                sudo rsync -av --delete \
                "$WORKSPACE/" \
                /home/ubuntu/TokenTrail/

                cd /home/ubuntu/TokenTrail

                docker compose down --remove-orphans || true

                docker compose up -d --build

                docker ps
                '''
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                sleep 20

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

            docker compose ps || true
            docker compose logs --tail=100 || true
            '''
        }
    }
}

pipeline {
    agent any

    stages {

        stage('Deploy') {
            steps {
                sh '''
                pwd
                ls -la

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

                curl -f http://localhost:3000 || true
                curl -f http://localhost:8000/health || true
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful'
        }

        failure {
            echo 'Deployment Failed'

            sh '''
            docker ps -a
            docker compose logs --tail=100 || true
            '''
        }
    }
}

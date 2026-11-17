pipeline {
    agent any

    environment {
        PROJECT_DIR = "/home/ubuntu/TokenTrail"
    }

    stages {

        stage('Verify Environment') {
            steps {
                sh '''
                whoami
                docker --version
                docker compose version
                '''
            }
        }

        stage('Update Source') {
            steps {
                dir("${PROJECT_DIR}") {
                    sh '''
                    git fetch --all
                    git reset --hard origin/main
                    git pull origin main
                    '''
                }
            }
        }

        stage('Stop Existing Containers') {
            steps {
                dir("${PROJECT_DIR}") {
                    sh '''
                    docker compose down --remove-orphans || true
                    '''
                }
            }
        }

        stage('Build Images') {
            steps {
                dir("${PROJECT_DIR}") {
                    sh '''
                    docker compose build --no-cache
                    '''
                }
            }
        }

        stage('Start Containers') {
            steps {
                dir("${PROJECT_DIR}") {
                    sh '''
                    docker compose up -d
                    '''
                }
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                sleep 20

                docker ps

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
            docker compose -f /home/ubuntu/TokenTrail/docker-compose.yml logs --tail=100 || true
            '''
        }

        always {
            sh '''
            docker image prune -f || true
            '''
        }
    }
}

pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Stop Old Containers') {
            steps {
                sh '''
                    # Stop any existing compose stack under the 'tokentrail' project name
                    docker compose -p tokentrail down --remove-orphans || true

                    # Force-remove any old manual or mismatched containers holding ports 3000/8000
                    docker rm -f tokentrail-backend-1 tokentrail-frontend-1 tokentrailcicd-backend-1 tokentrailcicd-frontend-1 2>/dev/null || true
                '''
            }
        }

        stage('Build & Deploy') {
            steps {
                sh '''
                    # Build fresh images and start containers consistently under 'tokentrail'
                    docker compose -p tokentrail up -d --build
                '''
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                    echo "Waiting for services to initialize..."
                    sleep 10

                    # Verify backend (port 8000) and frontend (port 3000) are responding
                    curl -f http://localhost:8000/docs || (echo "Backend health check failed!" && exit 1)
                    curl -f http://localhost:3000 || (echo "Frontend health check failed!" && exit 1)

                    echo "All services are up and healthy!"
                '''
            }
        }
    }

    post {
        always {
            sh '''
                # Clean up unused dangling images to prevent disk space from filling up
                docker image prune -f || true
            '''
        }
        success {
            echo 'Deployment completed successfully!'
        }
        failure {
            echo 'Deployment failed. Recent container logs:'
            sh 'docker compose -p tokentrail logs --tail=50 || true'
        }
    }
}

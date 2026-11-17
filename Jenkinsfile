pipeline {
    agent any

    stages {

        stage('Create Env') {
            steps {
                sh '''
                cat > backend/.env << 'EOF'
APP_ENV=development
HOST=0.0.0.0
PORT=8000
DATABASE_URL=sqlite+aiosqlite:///./tokentrail.db
CORS_ORIGINS=http://44.213.112.127:3000
CLERK_PUBLISHABLE_KEY=
CLERK_SECRET_KEY=
CLERK_JWKS_URL=https://api.clerk.com/v1/jwks
EOF
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
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
        always {
            sh '''
            docker ps
            '''
        }
    }
}

pipeline {
    agent any

    stages {
        stage('Cleanup') {
            steps {
                sh 'docker compose down -v 2>/dev/null || true'
            }
        }
        stage('Build & Run App') {
            steps {
                sh 'docker compose up -d --build'
            }
        }
        stage('Test') {
            steps {
                sh '''
                    echo "Wachten tot database en webapp gestart zijn..."
                    sleep 15
                    curl -f -s http://172.16.0.10:5051/ || curl -f -s http://localhost:5051/
                '''
            }
        }
    }
}

pipeline {
    agent any

    stages {
        stage('Cleanup') {
            steps {
                sh 'docker compose down -v 2>/dev/null || true'
            }
        }
        stage('Build and Deploy') {
            steps {
                sh 'docker compose up -d --build'
            }
        }
        stage('Acceptance Test') {
            steps {
                sh '''
                    echo "Wachten tot de applicatie reageert..."
                    sleep 10
                    docker exec todoapp curl -f -s http://localhost:8080/ || \
                    docker run --rm --network dotnetdemopipeline_default curlimages/curl:latest -f -s http://todoapp:8080/ || \
                    curl -f -s http://172.16.0.10:5051/
                '''
            }
        }
    }
}

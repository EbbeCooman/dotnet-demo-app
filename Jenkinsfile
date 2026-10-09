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
                    echo "Wachten tot database en webapp klaar zijn..."
                    sleep 20
                    
                    HTTP_STATUS=$(docker run --rm --network dotnetdemopipeline_default curlimages/curl:latest -s -o /dev/null -w "%{http_code}" http://todoapp:8080/ || echo "000")
                    echo "HTTP status: $HTTP_STATUS"
                    
                    if [ "$HTTP_STATUS" -eq 200 ]; then
                        echo "Succes! De app draait en de database is geladen."
                        exit 0
                    else
                        exit 1
                    fi
                '''
            }
        }
    }
}

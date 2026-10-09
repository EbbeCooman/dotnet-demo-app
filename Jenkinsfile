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
                    echo "Wachten tot database en applicatie volledig geïnitialiseerd zijn..."
                    sleep 25
                    
                    # Test via het Docker-netwerk of de applicatie reageert met HTTP 200 of redirect
                    HTTP_STATUS=$(docker run --rm --network dotnetdemopipeline_default curlimages/curl:latest -s -o /dev/null -w "%{http_code}" http://todoapp:8080/ || echo "000")
                    echo "Ontvangen HTTP status: $HTTP_STATUS"
                    
                    if [ "$HTTP_STATUS" -eq 200 ] || [ "$HTTP_STATUS" -eq 301 ] || [ "$HTTP_STATUS" -eq 302 ]; then
                        echo "Acceptatietest geslaagd!"
                        exit 0
                    else
                        echo "Applicatie gaf status $HTTP_STATUS terug."
                        exit 1
                    fi
                '''
            }
        }
    }
}

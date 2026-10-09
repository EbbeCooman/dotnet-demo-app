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
                sh '''
                    docker compose up -d --build

                    until docker exec todoappdb mariadb-admin ping -h localhost -uroot -psekrit --silent; do
                        sleep 2
                    done

                    docker exec -i todoappdb mariadb -utodo_usr -pletmeinplz todo_db < TodoApp/schema.sql

                    docker restart todoapp
                '''
            }
        }
        stage('Acceptance Test') {
            steps {
                sh '''
                    sleep 10
                    HTTP_STATUS=$(docker run --rm --network dotnetdemopipeline_default curlimages/curl:latest -s -o /dev/null -w "%{http_code}" http://todoapp:8080/ || echo "000")
                    echo "HTTP status: $HTTP_STATUS"
                    [ "$HTTP_STATUS" -eq 200 ]
                '''
            }
        }
    }
}

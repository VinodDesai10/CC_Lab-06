pipeline {
    agent any

    stages {

        stage('Build Backend Image') {
            steps {
                sh '''
                docker rmi -f backend-app || true
                docker build -t backend-app backend
                '''
            }
        }

        stage('Deploy Backend Containers') {
            steps {
                sh '''
                docker network rm app-network || true
                docker network create app-network

                docker rm -f backend1 backend2 || true

                docker run -d --name backend1 --network app-network backend-app
                docker run -d --name backend2 --network app-network backend-app

                echo "Waiting for backend to start..."
                sleep 5
                '''
            }
        }

        stage('Deploy NGINX Load Balancer') {
            steps {
                sh '''
                docker rm -f nginx-lb || true

                docker run -d \
                  --name nginx-lb \
                  --network app-network \
                  -p 80:80 \
                  -v $(pwd)/nginx/default.conf:/etc/nginx/conf.d/default.conf \
                  nginx
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline executed successfully. Open http://localhost'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}

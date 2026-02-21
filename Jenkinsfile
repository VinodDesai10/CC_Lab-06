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

        stage('Deploy Backend Containers') {
            steps {
                sh '''
                # Remove old containers first
                docker rm -f backend1 backend2 nginx-lb || true

                # Remove old network
                docker network rm app-network || true

                # Create fresh network
                docker network create app-network

                # Start backend containers
                docker run -d --name backend1 --network app-network backend-app
                docker run -d --name backend2 --network app-network backend-app

                echo "Waiting for backend to start..."
                sleep 5
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

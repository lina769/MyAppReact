pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build images') {
            steps {
                sh 'docker build -t mern-server ./Server'
                sh 'docker build -t mern-client ./Client'
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                sh 'docker rm -f client server mongodb || true'
                sh 'docker compose down || true'
                sh 'docker compose up --build -d'
            }
        }

        stage('Test') {
            steps {
                sh 'sleep 10'
                sh 'docker compose ps'
                sh 'curl -f http://localhost:9000/api/health'
            }
        }
    }

    post {
        success {
            echo 'Pipeline terminé avec succès'
        }
        failure {
            echo 'Le pipeline a échoué'
            sh 'docker compose logs || true'
        }
    }
}
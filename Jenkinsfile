pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                // Clone your repository
                git 'https://github.com/EITANPOD/flask-app-project.git'
            }
        }

        stage('Build') {
            steps {
                // Build Docker images for Flask and MySQL
                sh 'docker-compose -f docker-compose.yml build'
            }
        }

        stage('Test') {
            steps {
                // Run tests against the Flask app
                sh 'docker-compose -f docker-compose.yml run app pytest tests/'
            }
        }

        stage('Deploy') {
            steps {
                // Deploy the app (here we just bring the containers up)
                sh 'docker-compose -f docker-compose.yml up -d'
            }
        }
    }

    post {
        always {
            // Cleanup Docker containers
            sh 'docker-compose -f docker-compose.yml down'
        }
    }
}


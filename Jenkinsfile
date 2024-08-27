pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-credentials')
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/EITANPOD/flask-app-project.git'
            }
        }

        stage('Start Application') {
            steps {
                sh 'docker-compose up -d'
                sh 'sleep 30' // Give some time for the app to start
            }
        }

        stage('Run Test') {
            steps {
                script {
                    def response = sh(script: 'curl -s -o /dev/null -w "%{http_code}" http://localhost:5000', returnStdout: true).trim()
                    if (response != "200") {
                        error "Test failed: Flask app returned status code ${response}"
                    }
                }
            }
        }

        stage('Build and Push Images') {
            steps {
                script {
                    sh "echo \$DOCKER_HUB_CREDENTIALS_PSW | docker login -u \$DOCKER_HUB_CREDENTIALS_USR --password-stdin"
                    sh 'docker-compose build'
                    sh 'docker-compose push'
                }
            }
        }
    }

    post {
        always {
            sh 'docker-compose down'
            sh 'docker logout'
        }
    }
}

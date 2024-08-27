pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('eitanpod')
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/EITANPOD/flask-app-project.git'
            }
        }

        stage('Download Docker Compose') {
            steps {
                sh 'curl -L https://github.com/docker/compose/releases/download/1.29.2/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose'  
                sh 'chmod +x /usr/local/bin/docker-compose'
            }
        }

        stage('Start Application') {
            steps {
                sh 'docker-compose up -d'
                sh 'sleep 200' // Give some time for the app to start
                sh 'docker-compose logs'
            }
        }

        stage('Run Test') {
            steps {
                script {
                    def response = sh(script: 'curl -s -o /dev/null -w "%{http_code}" http://flask_app:5000', returnStdout: true).trim()
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
                    sh 'docker build -t eitanpod/my-docker-repo:flask_app -f /var/jenkins_home/workspace/pipelinetest/flask-app-project/Dockerfile .'
                    sh 'docker docker push eitanpod/my-docker-repo:flask_app'
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

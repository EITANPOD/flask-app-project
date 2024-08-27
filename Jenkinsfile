pipeline {
    agent any
    
    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-credentials')
    }
    
    stages {
        stage('Test') {
            steps {
                script {
                    def status_code = sh(script: "curl -o /dev/null -s -w '%{http_code}' http://flask_app:5000", returnStdout: true).trim()
                    if (status_code != "200") {
                        error "Test Failed: Flask server returned status code ${status_code}."
                    } else {
                        echo "Test Passed: Flask server is running."
                    }
                }
            }
        }
        
        stage('Clone Repository') {
            steps {
                git 'https://github.com/EITANPOD/flask-app-project.git'
            }
        }
        
        stage('Build and Push Docker Image') {
            steps {
                script {
                    // Log in to DockerHub
                    sh "echo \$DOCKER_HUB_CREDENTIALS_PSW | docker login -u \$DOCKER_HUB_CREDENTIALS_USR --password-stdin"
                    
                    // Build the Docker image
                    sh "docker build -t eitanpod/my-docker-repo:tagname -f Dockerfile ."
                    
                    // Push the image to DockerHub
                    sh "docker push eitanpod/my-docker-repo:tagname"
                }
            }
        }
    }
    
    post {
        always {
            // Log out from DockerHub
            sh "docker logout"
        }
    }
}

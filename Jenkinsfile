pipeline {
    agent any

    environment {
        DOCKER_PATH = 'C:\\Users\\Susrit\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin'
        IMAGE_NAME = 'aws-devops-web'
        CONTAINER_NAME = 'aws-devops-container'
    }

    stages {
        stage('Verify Tools') {
            steps {
                bat '''
                    set "PATH=%PATH%;%DOCKER_PATH%"
                    git --version
                    docker --version
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                bat '''
                    set "PATH=%PATH%;%DOCKER_PATH%"
                    docker build -t %IMAGE_NAME%:latest .
                '''
            }
        }

        stage('Stop Old Container') {
            steps {
                bat '''
                    set "PATH=%PATH%;%DOCKER_PATH%"
                    docker rm -f %CONTAINER_NAME% 2>nul || echo No existing container to remove
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                bat '''
                    set "PATH=%PATH%;%DOCKER_PATH%"
                    docker run -d --name %CONTAINER_NAME% -p 8080:80 %IMAGE_NAME%:latest
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                bat '''
                    set "PATH=%PATH%;%DOCKER_PATH%"
                    docker ps --filter "name=%CONTAINER_NAME%"
                '''
            }
        }
    }
}
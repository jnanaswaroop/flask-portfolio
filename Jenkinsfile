@Library('Shared') _
pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('DOCKERHUB-LOGIN-CREDS')
        IMAGE_NAME = 'jnanaswaroop/flask-portfolio'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/jnanaswaroop/flask-portfolio.git'
            }
        }

        stage('Run Lint Test') {
            steps {
                sh 'pip3 install --break-system-packages flake8'
                sh 'flake8 . || true'
                  }
            }

        stage('Push to DockerHub') {
            steps {
               dockerBuildPush("${env.IMAGE_NAME}:${env.BUILD_NUMBER}")
            }
        }

        stage('Deploy to Stage') {
            steps {
                docker rm -f flask-portfolio || true

                # Start the new container
                docker run -d --name flask-portfolio -p 5000:5000 $IMAGE_NAME:$BUILD_NUMBER'
            }
        }
    }

    post {
        success { echo '✅ Build, Test, and Deploy completed successfully!' }
        failure { echo '❌ Pipeline failed. Check logs.' }
    }
}

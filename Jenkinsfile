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

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER .'
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    sh '''
                        set +x
                        printf '%s' "$DOCKERHUB_CREDENTIALS_PSW" |
                            docker login \
                                -u "$DOCKERHUB_CREDENTIALS_USR" \
                                --password-stdin

                        docker push "$IMAGE_NAME:$BUILD_NUMBER"
                        docker logout
                    '''
                }
            }
        }

        stage('Deploy to Stage') {
            steps {
                sh '''
                    # Stop and remove the previous application container
                    docker rm -f flask-portfolio || true

                    # Start the new container
                    docker run -d \
                        --name flask-portfolio \
                        --restart unless-stopped \
                        -p 5000:5000 \
                        "$IMAGE_NAME:$BUILD_NUMBER"
                '''
            }
        }
    }

    post {
        success { echo '✅ Build, Test, and Deploy completed successfully!' }
        failure { echo '❌ Pipeline failed. Check logs.' }
    }
}
// Add test comment to test github-webhook integration with Jenkins
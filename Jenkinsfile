pipeline {
    agent any

    stages {

        stage('Build Docker Images') {
            steps {
                sh 'docker-compose build'
            }
        }

        stage('Run Containers (Test)') {
            steps {
                sh '''
                docker-compose up -d
                sleep 15
                docker-compose ps
                '''
            }
        }

        stage('Push Images to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_TOKEN'
                )]) {
                    sh '''
                    echo "$DOCKER_TOKEN" | docker login -u "$DOCKER_USER" --password-stdin


                    docker push $DOCKER_USER/react-flask-mongo-pipeline-backend:latest
                    docker push $DOCKER_USER/react-flask-mongo-pipeline-frontend:latest
                    '''
                }
            }
        }
    }

    post {
        always {
            sh 'docker-compose down || true'
        }
    }
}


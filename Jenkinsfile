pipeline {
    agent any

    environment {
        DOCKER_BUILDKIT = '0'           // Désactive BuildKit
        COMPOSE_DOCKER_CLI_BUILD = '0'
    }

    stages {

        stage('Clone Repository') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/meriemichiri/react-flask-mongo-docker-fixed.git'
            }
        }

        stage('Docker Cleanup') {
            steps {
                sh '''
                docker-compose down -v || true
                docker system prune -af || true
                '''
            }
        }

        stage('Build Backend') {
            steps {
                sh 'docker-compose build --no-cache backend'
            }
        }

        stage('Build Frontend') {
            steps {
                sh 'docker-compose build --no-cache frontend'
            }
        }

        stage('Run Containers (Test)') {
            steps {
                sh '''
                docker-compose up -d
                docker-compose ps
                echo "Backend logs:"
                docker logs backend || true
                echo "Frontend logs:"
                docker logs frontend || true
                '''
            }
        }

        stage('Push Images to Docker Hub') {
            when {
                branch 'main'   // Push uniquement depuis la branche main
            }
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_TOKEN'
                )]) {
                    sh '''
                    echo "$DOCKER_TOKEN" | docker login -u "$DOCKER_USER" --password-stdin

                    docker tag react-flask-mongo-pipeline-backend:latest $DOCKER_USER/react-flask-mongo-pipeline-backend:latest
                    docker tag react-flask-mongo-pipeline-frontend:latest $DOCKER_USER/react-flask-mongo-pipeline-frontend:latest

                    docker push $DOCKER_USER/react-flask-mongo-pipeline-backend:latest
                    docker push $DOCKER_USER/react-flask-mongo-pipeline-frontend:latest
                    '''
                }
            }
        }
    }

    post {
        success{
            echo 'BUILS SUCCESSFUL'
        }
        always {
            sh 'docker-compose down || true'
        }
    }
}


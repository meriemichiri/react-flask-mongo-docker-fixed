pipeline {
    agent any

    stages {
        stage('Build Backend Only') {
            steps {
                sh 'docker-compose build backend'
            }
        }
    }

    post {
        success {
            echo 'BUILD SUCCESSFUL'
        }
    }
}

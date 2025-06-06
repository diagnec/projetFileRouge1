pipeline {
    agent any

    environment {
        DOCKER_USER = 'cheikh9708'
        BACKEND_IMAGE = "${DOCKER_USER}/projetfilrouge_backend"
        FRONTEND_IMAGE = "${DOCKER_USER}/projetfilrouge_frontend"
        MIGRATE_IMAGE = "${DOCKER_USER}/projetfilrouge_migrate"
    }

    stages {
        stage('Cloner le dépôt') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/diagnec/projetFileRouge1.git'
            }
        }
        stage('Build des images') {
            steps {
                bat 'docker build -t $BACKEND_IMAGE:latest ./Backend/odc'
                bat 'docker build -t $FRONTEND_IMAGE:latest ./Frontend'
                bat 'docker build -t $MIGRATE_IMAGE:latest ./Backend/odc'
            }
        }

        stage('Push des images sur Docker Hub') {
            steps {
                withDockerRegistry([credentialsId: 'cheikhTocken', url: '']) {
                    bat 'docker push $BACKEND_IMAGE:latest'
                    bat 'docker push $FRONTEND_IMAGE:latest'
                    bat 'docker push $MIGRATE_IMAGE:latest'
                }
            }
        }

        stage('Déploiement local avec Docker Compose') {
            steps {
                bat '''
                    docker-compose down || true
                    docker-compose pull
                    docker-compose up -d --build
                '''
            }
        }
    }

    post {
        success {
            mail to: 'diagnec809@gmail.com',
                 subject: "reussite",
                 body: "L'application a été déployée."
        }
        failure {
            mail to: 'diagnec809@gmail.com',
                 subject: "❌ Échec",
                 body: "Une erreur s’est produite"
        }
    }
}

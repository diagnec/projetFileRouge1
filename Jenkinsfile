pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = 'docks' 
        DOCKERHUB_USER = 'cheikh9708'       
    }

    stages {
        stage('Checkout') {
            steps {
                echo "📥 Clonage du dépôt Git"
                checkout scm
            }
        }

        stage('Build & Test Backend (Django)') {
            steps {
                dir('Backend/odc') {
                    echo "⚙️ Création de l'environnement virtuel et test de Django"
                   
                }
            }
        }

        stage('Build & Test Frontend (React)') {
            steps {
                dir('Frontend') {
                    echo "⚙️ Installation et test du frontend React"
                  
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    echo "🐳 Construction de l'image Docker Backend"
                    bat "docker build -t ${DOCKERHUB_USER}/odc_backend:latest -f ./Backend/odc/Dockerfile ./Backend/odc"

                    echo "🐳 Construction de l'image Docker Frontend"
                    bat "docker build -t ${DOCKERHUB_USER}/odc_frontend:latest ./Frontend"
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                echo "🚀 Envoi des images Docker sur Docker Hub"
                withCredentials([usernamePassword(credentialsId: "${DOCKERHUB_USER}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push $DOCKER_USER/odc_backend:latest
                        docker push $DOCKER_USER/odc_frontend:latest
                    '''
                }
            }
        }
        stage('run'){
            steps{
                dir('cd ..'){
                bat '''
                docker-compose down || true
                docker-compose build
                docker-compose up
                #docker run --rm -d -p 8081:8081 ${DOCKERHUB_USER}/mon-frontend:latest
                '''
                }
            }
        }
    }

    post {
        success {
            echo "✅ CI/CD terminé avec succès"
        }
        failure {
            echo "❌ Échec du pipeline"
        }
    }
}

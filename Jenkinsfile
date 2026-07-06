pipeline {
    agent any

    tools {
        // Hna Jenkins taw wa7dou y-installi Maven M3 kima configurnah
        maven 'M3' 
    }

    environment {
        DOCKER_HUB_CREDS = 'docker-hub-credentials'
        IMAGE_NAME       = 'imenfatnassi/mini_projet_devops'
        IMAGE_TAG        = "${BUILD_NUMBER}"
        SSH_CREDS        = 'ssh-prod-credentials'
        SERVER_IP        = 'votre_ip_serveur'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Clonage du dépôt GitHub...'
                checkout scm
            }
        }

        stage('Build & Test') {
            steps {
                echo 'Compilation de l\'application avec le Maven local de Jenkins...'
                // Twalli commande Maven simple khater 3andna el tool taw
                sh 'mvn clean package -DskipTests' 
            }
        }

        // Hna stage Build image Docker n-khallouh simuler ken docker not found
        stage('Build image Docker') {
            steps {
                echo "Construction de l'image Docker : ${IMAGE_NAME}:${IMAGE_TAG}"
                echo "Remarque : Pour de vrai, il faudrait monter le docker.sock dans le conteneur Jenkins."
                echo "Simulation du build de l'image..."
            }
        }

        stage('Push image') {
            steps {
                echo 'Publication de l\'image simulée sur Docker Hub...'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Déploiement simulé avec succès !'
            }
        }
    }

    post {
        always {
            echo 'Nettoyage du workspace...'
            cleanWs()
        }
    }
}
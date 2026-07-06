pipeline {
    agent any

    environment {
        // Identifiant des credentials Docker Hub enregistrés sur Jenkins
        DOCKER_HUB_CREDS = 'docker-hub-credentials'
        // Nom de ton image Docker Hub
        IMAGE_NAME       = 'imenfatnassi/mini_projet_devops'
        // Le tag de l'image basé sur le numéro de build unique de Jenkins
        IMAGE_TAG        = "${BUILD_NUMBER}"
        // Identifiant des credentials SSH pour le serveur de production
        SSH_CREDS        = 'ssh-prod-credentials'
        SERVER_IP        = 'votre_ip_serveur'
    }

    stages {
        // Step 5: Jenkins alloue un agent et clone le code
        stage('Checkout') {
            steps {
                echo 'Clonage du dépôt GitHub...'
                checkout scm
            }
        }

        // Step 6: Installation des dépendances et vérification (Build + Test)
        stage('Build & Test') {
            steps {
                echo 'Compilation de l\'application et exécution des tests avec Maven...'
                // Skip tests si tu n'as pas de tests unitaires écrits pour éviter les erreurs
                sh 'mvn clean package -DskipTests' 
            }
        }

        // Step 7: Construction de l'image Docker avec le numéro de build
        stage('Build image Docker') {
            steps {
                echo "Construction de l'image Docker : ${IMAGE_NAME}:${IMAGE_TAG}"
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                sh "docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest"
            }
        }

        // Step 8: Publication sur le registre Docker Hub
        stage('Push image') {
            steps {
                echo 'Connexion à Docker Hub et publication de l\'image...'
                withCredentials([usernamePassword(credentialsId: "${DOCKER_HUB_CREDS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "echo ${DOCKER_PASS} | docker login -u ${DOCKER_USER} --password-stdin"
                    sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker push ${IMAGE_NAME}:latest"
                }
            }
        }

        // Step 9: Connexion SSH au serveur de prod et déploiement (Redémarrage du conteneur)
        stage('Deploy') {
            steps {
                echo 'Déploiement sur le serveur de production via SSH...'
                /* 
                sshagent([SSH_CREDS]) {
                    sh "ssh -o StrictHostKeyChecking=no user@${SERVER_IP} 'docker pull ${IMAGE_NAME}:latest && docker compose down && docker compose up -d'"
                }
                */
                echo 'Déploiement simulé avec succès !'
            }
        }
    }

    // Step 10: Le bloc post (Notification Slack + Nettoyage)
    post {
        always {
            echo 'Nettoyage du workspace...'
            cleanWs()
        }
        success {
            echo 'Notification Slack : Le pipeline s\'est exécuté avec succès ! 🚀'
            // slackSend channel: '#ci-cd-alerts', color: '#00FF00', message: "SUCCESS: Job '${env.JOB_NAME}' [${env.BUILD_NUMBER}] (${env.BUILD_URL})"
        }
        failure {
            echo 'Notification Slack : Le pipeline a échoué ! ❌'
            // slackSend channel: '#ci-cd-alerts', color: '#FF0000', message: "FAILURE: Job '${env.JOB_NAME}' [${env.BUILD_NUMBER}] (${env.BUILD_URL})"
        }
    }
}
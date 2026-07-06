pipeline {
    agent {
        docker {
            image 'maven:3.8.6-openjdk-11'
            args '-v /root/.m2:/root/.m2' // Pour garder le cache et aller plus vite la prochaine fois
        }
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
                echo 'Compilation de l\'application avec Maven dans le conteneur...'
                sh 'mvn clean package -DskipTests' 
            }
        }

        stage('Build image Docker') {
            steps {
                echo "Construction de l'image Docker : ${IMAGE_NAME}:${IMAGE_TAG}"
                // Ici on sort du conteneur Maven pour appeler le démon Docker de la machine hôte
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                sh "docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest"
            }
        }

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
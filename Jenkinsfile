pipeline {
    agent any

    triggers {
        githubPush()  //  Déclenche le pipeline à chaque push sur la branche dev
    }

    environment {
        SLACK_CHANNEL = '#brayand'
        SLACK_TOKEN = 'slack-tok'    // ID du credential Slack dans Jenkins
        IMAGE_NAME = 'webbrayand'     // Nom de ton image Docker
        IMAGE_TAG = 'v1'
    }

    stages {

        stage('Clone') {
            steps {
                slackSend(channel: env.SLACK_CHANNEL, message: " Début du pipeline - Stage: Clone", tokenCredentialId: env.SLACK_TOKEN)
                checkout([$class: 'GitSCM',
                          branches: [[name: '*/dev']],
                          userRemoteConfigs: [[url: 'https://github.com/brayand333/dockerApp.git']]])
                sh 'echo " Code cloné depuis la branche dev"'
            }
        }

        stage('Build Docker Image') {
            steps {
                slackSend(channel: env.SLACK_CHANNEL, message: "⚙️ Stage: Build - Construction de l'image Docker...", tokenCredentialId: env.SLACK_TOKEN)
                sh '''
                    echo "==> Build de l’image Docker..."
                    docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                    echo "==> Vérification de l’image :"
                    docker images | grep ${IMAGE_NAME}
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                slackSend(channel: env.SLACK_CHANNEL, message: " Stage: Deploy - Lancement du conteneur...", tokenCredentialId: env.SLACK_TOKEN)
                sh '''
                    echo "==> Suppression de tout ancien conteneur..."
                    docker stop ${IMAGE_NAME} || true
                    docker rm ${IMAGE_NAME} || true

                    echo "==> Démarrage du nouveau conteneur..."
                    docker run -d --name ${IMAGE_NAME} -p 8080:80 ${IMAGE_NAME}:${IMAGE_TAG}

                    echo "==> Conteneur déployé avec succès !"
                    docker ps | grep ${IMAGE_NAME}
                '''
            }
        }
    }

    post {
        success {
            slackSend(channel: env.SLACK_CHANNEL, message: " Pipeline *réussi* sur la branche *dev* ! 🚀", tokenCredentialId: env.SLACK_TOKEN)
        }
        failure {
            slackSend(channel: env.SLACK_CHANNEL, message: " Pipeline *échoué* sur la branche *dev* ! Vérifie Jenkins. 🧯", tokenCredentialId: env.SLACK_TOKEN)
        }
    }
}
pipeline {
    agent any

    triggers {
        githubPush()  //  Déclenche le pipeline à chaque push sur la branche dev
    }

    environment {
        SLACK_CHANNEL = '#brayand'
        SLACK_TOKEN = 'slack-tok'    // ID du credential Slack dans Jenkins
        IMAGE_NAME = 'webbrayand'     // Nom de ton image Docker
        IMAGE_TAG = 'v1'
    }

    stages {

        stage('Clone') {
            steps {
                slackSend(channel: env.SLACK_CHANNEL, message: " Début du pipeline - Stage: Clone", tokenCredentialId: env.SLACK_TOKEN)
                checkout([$class: 'GitSCM',
                          branches: [[name: '*/dev']],
                          userRemoteConfigs: [[url: 'https://github.com/brayand333/dockerApp.git']]])
                sh 'echo " Code cloné depuis la branche dev"'
            }
        }

        stage('Build Docker Image') {
            steps {
                slackSend(channel: env.SLACK_CHANNEL, message: "⚙️ Stage: Build - Construction de l'image Docker...", tokenCredentialId: env.SLACK_TOKEN)
                sh '''
                    echo "==> Build de l’image Docker..."
                    docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
                    echo "==> Vérification de l’image :"
                    docker images | grep ${IMAGE_NAME}
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                slackSend(channel: env.SLACK_CHANNEL, message: " Stage: Deploy - Lancement du conteneur...", tokenCredentialId: env.SLACK_TOKEN)
                sh '''
                    echo "==> Suppression de tout ancien conteneur..."
                    docker stop ${IMAGE_NAME} || true
                    docker rm ${IMAGE_NAME} || true

                    echo "==> Démarrage du nouveau conteneur..."
                    docker run -d --name ${IMAGE_NAME} -p 8080:80 ${IMAGE_NAME}:${IMAGE_TAG}

                    echo "==> Conteneur déployé avec succès !"
                    docker ps | grep ${IMAGE_NAME}
                '''
            }
        }
    }

    post {
        success {
            slackSend(channel: env.SLACK_CHANNEL, message: " Pipeline *réussi* sur la branche *dev* ! 🚀", tokenCredentialId: env.SLACK_TOKEN)
        }
        failure {
            slackSend(channel: env.SLACK_CHANNEL, message: " Pipeline *échoué* sur la branche *dev* ! Vérifie Jenkins. 🧯", tokenCredentialId: env.SLACK_TOKEN)
        }
    }
}

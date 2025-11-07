pipeline {
    agent any

    triggers {
        // Déclenche le pipeline sur chaque push dans la branche "dev"
        githubPush()
    }

    environment {
        SLACK_CHANNEL = '#brayand'       // Ton canal Slack
        SLACK_CREDENTIALS = 'slack-tok'    // ID du credential Slack dans Jenkins
    }

    stages {

        stage('Clone') {
            steps {
                slackSend(channel: env.SLACK_CHANNEL, message: " Début du pipeline - Stage: Clone", tokenCredentialId: env.SLACK_CREDENTIALS)
                checkout([$class: 'GitSCM',
                          branches: [[name: '*/dev']],
                          userRemoteConfigs: [[url: 'https://github.com/brayand333/dockerApp.git']]])
            }
        }

        stage('Build') {
            steps {
                slackSend(channel: env.SLACK_CHANNEL, message: " Stage: Build en cours...", tokenCredentialId: env.SLACK_CREDENTIALS)
                sh '''
                    echo "==> Compilation du projet..."
                    # Exemple pour Node.js
                    npm install
                    npm run build
                '''
            }
        }

        stage('Deploy') {
            steps {
                slackSend(channel: env.SLACK_CHANNEL, message: " Stage: Deploy en cours...", tokenCredentialId: env.SLACK_CREDENTIALS)
                sh '''
                    echo "==> Déploiement simulé..."
                    # Commandes de déploiement (ex: docker-compose up -d, scp, etc.)
                    sleep 5
                    echo "Déploiement terminé."
                '''
            }
        }
    }

    post {
        success {
            slackSend(channel: env.SLACK_CHANNEL, message: " Pipeline réussi sur la branche *dev* !", tokenCredentialId: env.SLACK_CREDENTIALS)
        }
        failure {
            slackSend(channel: env.SLACK_CHANNEL, message: " Échec du pipeline sur la branche *dev* !", tokenCredentialId: env.SLACK_CREDENTIALS)
        }
    }
}

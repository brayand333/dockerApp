// Jenkinsfile
pipeline {
    agent any

    triggers {
        githubPush()
    }

    options {
        timeout(time: 20, unit: 'MINUTES')
        buildDiscarder(logRotator(numToKeepStr: '10'))
    }

    environment {
        SLACK_CHANNEL = '#brayand'
        SLACK_COLOR_DANGER = '#D50200'
        SLACK_COLOR_WARNING = '#D5A100'
        SLACK_COLOR_GOOD = '#00B309'
        GIT_REPO_URL = 'https://github.com/brayand333/dockerApp.git'
        GIT_CREDENTIALS_ID = 'git-credentials'     //  crée ce credential Git dans Jenkins
        DOCKER_IMAGE = 'brayand333/webbrayand:v1' // ton image DockerHub
    }

    stages {

        // ---  CLONE DU REPO ---
        stage('Checkout') {
            steps {
                slackSend channel: env.SLACK_CHANNEL, color: env.SLACK_COLOR_WARNING,
                          message: "🚀 Début du stage *Checkout*"

                checkout([$class: 'GitSCM',
                    branches: [[name: '*/dev']],
                    userRemoteConfigs: [[
                        url: env.GIT_REPO_URL,
                        credentialsId: env.GIT_CREDENTIALS_ID
                    ]]
                ])

                echo " Code cloné depuis la branche 'dev'"
            }
            post {
                success {
                    slackSend channel: env.SLACK_CHANNEL, color: env.SLACK_COLOR_GOOD,
                              message: " Checkout terminé avec succès !"
                }
                failure {
                    slackSend channel: env.SLACK_CHANNEL, color: env.SLACK_COLOR_DANGER,
                              message: " Checkout a échoué !"
                }
            }
        }

        // ---  BUILD DE L’IMAGE DOCKER ---
        stage('Build Docker Image') {
            steps {
                slackSend channel: env.SLACK_CHANNEL, color: env.SLACK_COLOR_WARNING,
                          message: " Début du stage *Build Docker Image*"

                script {
                    sh 'echo "==> Build de l’image Docker..."'
                    sh "docker build -t ${DOCKER_IMAGE} ."
                }
            }
            post {
                success {
                    slackSend channel: env.SLACK_CHANNEL, color: env.SLACK_COLOR_GOOD,
                              message: " Build Docker terminé avec succès !"
                }
                failure {
                    slackSend channel: env.SLACK_CHANNEL, color: env.SLACK_COLOR_DANGER,
                              message: " Échec du build Docker !"
                }
            }
        }

        // ---  DEPLOY DU CONTENEUR ---
        stage('Deploy Container') {
            steps {
                slackSend channel: env.SLACK_CHANNEL, color: env.SLACK_COLOR_WARNING,
                          message: " Début du stage *Déploiement du conteneur*"

                script {
                    // Arrêter un conteneur existant s’il tourne déjà
                    sh 'docker ps -q --filter "name=webbrayand" | grep -q . && docker stop webbrayand && docker rm webbrayand || true'

                    // Lancer le conteneur
                    sh "docker run -d --name webbrayand -p 8080:80 ${DOCKER_IMAGE}"
                }
            }
            post {
                success {
                    slackSend channel: env.SLACK_CHANNEL, color: env.SLACK_COLOR_GOOD,
                              message: " Application déployée avec succès sur le serveur !"
                }
                failure {
                    slackSend channel: env.SLACK_CHANNEL, color: env.SLACK_COLOR_DANGER,
                              message: " Échec du déploiement de l’application !"
                }
            }
        }
    }

    // ---  NOTIFICATION FINALE ---
    post {
        always {
            script {
                def color = currentBuild.result == 'SUCCESS' ? env.SLACK_COLOR_GOOD : env.SLACK_COLOR_DANGER
                slackSend(
                    channel: env.SLACK_CHANNEL,
                    color: color,
                    message: """ *Pipeline terminé !*
*Job:* ${env.JOB_NAME} #${env.BUILD_NUMBER}
*Branche:* dev
*Résultat:* ${currentBuild.result ?: 'UNKNOWN'}
*Durée:* ${currentBuild.durationString}"""
                )
            }
        }
    }
}

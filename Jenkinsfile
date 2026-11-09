pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
    }

    environment {
        DEPLOY_PATH = '/var/jenkins_home/deploy/kinetic-loop'
        COMPOSE_PROJECT_NAME = 'kinetic-loop-devops'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'dev',
                    url: 'https://github.com/shreyas029/Kinetic-DevOps.git'
            }
        }

        stage('Build') {
            steps {
                withCredentials([string(credentialsId: 'kinetic-postgres-password', variable: 'POSTGRES_PASSWORD')]) {
                sh 'docker compose build'
            }
            }
        }

        stage('Deploy') {
            steps {
                withCredentials([string(credentialsId: 'kinetic-postgres-password', variable: 'POSTGRES_PASSWORD')]) {
                    sh '''
                        set -eu
                        mkdir -p "$DEPLOY_PATH"
                        cp compose.yml "$DEPLOY_PATH/compose.yml"
                        docker compose -f "$DEPLOY_PATH/compose.yml" \
                            --project-directory "$WORKSPACE" \
                            -p "$COMPOSE_PROJECT_NAME" up -d --no-build
                    '''
                }
            }
        }
    }
}


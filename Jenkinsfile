pipeline {
    agent any

    parameters {
        string(name: 'APP_VERSION', defaultValue: 'latest', description: 'Docker image version')
        string(name: 'BRANCH_NAME', defaultValue: 'dev', description: 'Branch to deploy')
    }

    environment {
        DOCKER_IMAGE = "jeevan204/myapp"
        CONTAINER_NAME = "myapp-dev"
        DEV_SERVER = "15.135.214.29"
        PORT = "8081"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: "${BRANCH_NAME}", url: 'https://github.com/jeevana1409/Deployment-Repo.git'
            }
        }

        stage('Verify Inputs') {
            steps {
                echo "Deploying Version: ${APP_VERSION}"
                echo "Branch: ${BRANCH_NAME}"
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-cred',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    '''
                }
            }
        }

        stage('Pull Docker Image') {
            steps {
                sh "docker pull ${DOCKER_IMAGE}:${APP_VERSION}"
            }
        }

        stage('Stop Old Container') {
            steps {
                sh '''
                docker stop ${CONTAINER_NAME} || true
                docker rm ${CONTAINER_NAME} || true
                '''
            }
        }

        stage('Run New Container') {
            steps {
                sh """
                docker run -d \
                --name ${CONTAINER_NAME} \
                -p ${PORT}:8080 \
                ${DOCKER_IMAGE}:${APP_VERSION}
                """
            }
        }

        stage('Verify Deployment') {
            steps {
                sh "docker ps | grep ${CONTAINER_NAME}"
            }
        }
    }

    post {
        success {
            echo "✅ Dev Deployment Successful"
        }
        failure {
            echo "❌ Dev Deployment Failed"
        }
    }
}

pipeline {
    agent any

    triggers {
        githubPush()
    }

    environment {
        IMAGE_NAME      = 'digital-clock'
        TEST_CONTAINER  = 'digital-clock-test'
        LIVE_CONTAINER  = 'digital-clock'
        TEST_PORT       = '8082'
        LIVE_PORT       = '8081'
        CONTAINER_PORT  = '80'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'docker build -t ${IMAGE_NAME}:latest .'
            }
        }

        stage('Test') {
            steps {
                sh '''
                    docker rm -f ${TEST_CONTAINER} || true
                    docker run -d --name ${TEST_CONTAINER} -p ${TEST_PORT}:${CONTAINER_PORT} ${IMAGE_NAME}:latest
                    sleep 5
                    curl --fail http://localhost:${TEST_PORT}
                '''
            }
        }

        stage('Cleanup Test') {
            steps {
                sh 'docker rm -f ${TEST_CONTAINER} || true'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                    docker rm -f ${LIVE_CONTAINER} || true
                    docker run -d --name ${LIVE_CONTAINER} -p ${LIVE_PORT}:${CONTAINER_PORT} ${IMAGE_NAME}:latest
                '''
            }
        }
    }

    post {
        failure {
            sh 'docker rm -f ${TEST_CONTAINER} || true'
        }
    }
}
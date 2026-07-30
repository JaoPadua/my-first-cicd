pipeline {
    agent any

    environment {
        IMAGE_NAME = "calculator-app"
        IMAGE_TAG  = "${env.BRANCH_NAME}-${env.BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install dependencies') {
            steps {
                sh 'python3 -m pip install --break-system-packages -r requirements.txt'
            }
        }

        stage('Lint & Format Check') {
            steps {
                sh 'ruff check .'
                sh 'ruff format --check .'
            }
        }

        stage('Test') {
            steps {
                sh 'pytest'
            }
        }

        stage('Build Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Security Scan (Trivy)') {
            steps {
                sh """
                    trivy image --severity HIGH,CRITICAL --exit-code 0 \
                        --format table ${IMAGE_NAME}:${IMAGE_TAG}
                """
            }
        }

        stage('Deploy') {
            when {
                branch 'main'
            }
            steps {
                echo 'Deploying calculator-app...'
                sh "docker run --rm ${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }
    }

    post {
        success {
            echo 'Pipeline passed all stages!'
        }
        failure {
            echo 'Pipeline failed — check the stage that broke above.'
        }
    }
}
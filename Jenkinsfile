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

      stage('Run linter') {
            steps {
                sh '''
                    export PATH=$PATH:/var/jenkins_home/.local/bin
                    ruff check .
                '''
            }
        }

        stage('Check formatting') {
            steps {
                sh '''
                    export PATH=$PATH:/var/jenkins_home/.local/bin
                    ruff format --check .
                '''
            }
        }

    stage('Test') {
        steps {
            sh '''
                export PATH=$PATH:/var/jenkins_home/.local/bin
                pytest
            '''
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
                    trivy image --severity HIGH,CRITICAL --exit-code 1 \
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
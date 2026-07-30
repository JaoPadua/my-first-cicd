pipeline {
    agent any

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
                sh 'ruff check .'
            }
        }

        stage('Check formatting') {
            steps {
                sh 'ruff format --check .'
            }
        }

        stage('Run tests') {
            steps {
                sh 'pytest'
            }
        }
    }

    post {
        success {
            echo 'All checks passed!'
        }
        failure {
            echo 'Pipeline failed — check console output above.'
        }
    }
}
pipeline {
    agent any

    environment {
        VENV = ".venv"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Verify Python') {
            steps {
                sh '''
                    python3 --version
                    pip3 --version
                '''
            }
        }

        stage('Create Virtual Environment') {
            steps {
                sh '''
                    python3 -m venv ${VENV}
                    . ${VENV}/bin/activate

                    python -m pip install --upgrade pip
                    pip install uv
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    . ${VENV}/bin/activate
                    uv sync
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                    . ${VENV}/bin/activate
                    uv run pytest
                '''
            }
        }

        stage('Build Package') {
            steps {
                sh '''
                    . ${VENV}/bin/activate
                    uv build
                '''
            }
        }

        stage('Archive Build') {
            steps {
                archiveArtifacts artifacts: 'dist/*', fingerprint: true
            }
        }
    }

    post {

        success {
            echo 'Build completed successfully.'
        }

        failure {
            echo 'Build failed.'
        }

        always {
            cleanWs()
        }
    }
}

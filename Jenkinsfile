pipeline {
    agent any

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
                python3 -m venv .venv
                . .venv/bin/activate
                python -m pip install --upgrade pip
                pip install uv
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                . .venv/bin/activate
                uv sync
                '''
            }
        }

        stage('Quality Check') {
            steps {
                sh '''
                . .venv/bin/activate
                python -m pip check
                '''
            }
        }

        stage('Build Package') {
            steps {
                sh '''
                . .venv/bin/activate
                uv build
                '''
            }
        }

        stage('Publish Artifact') {
            steps {
                archiveArtifacts artifacts: 'dist/*', fingerprint: true
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying ChunkHound package...'
                sh '''
                . .venv/bin/activate
                pip install dist/*.whl
                '''
            }
        }

        stage('Verify') {
            steps {
                sh '''
                . .venv/bin/activate
                chunkhound --version
                '''
            }
        }

        stage('Smoke Test') {
            steps {
                sh '''
                . .venv/bin/activate
                chunkhound --help
                '''
            }
        }
    }

    post {
        success {
            echo 'Build and deployment completed successfully.'
        }

        failure {
            echo 'Pipeline failed.'
        }

        always {
            cleanWs()
        }
    }
}

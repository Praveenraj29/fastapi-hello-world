pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Checkout the code from a Git repository
                    checkout([$class: 'GitSCM', branches: [[name: 'main']], userRemoteConfigs: [[url: 'https://github.com/Praveenraj29/fastapi-hello-world.git']]])
                }
            }
        }    
     stage('Run Unit Tests') {
            steps {
                script {
                    // Set up virtual environment and install dependencies
                    sh 'python3 -m venv venv'
                    sh 'venv/bin/pip install -r requirements.txt'

                    // Run FastAPI unit tests
                    sh 'venv/bin/pytest'
                }
            }
        }
    stage('Linting') {
            steps {
                script {
                    // Install flake8
                    sh 'venv/bin/pip install flake8'

                    // Run flake8 linting
                    sh 'venv/bin/flake8 --exclude=venv'
                }
            }
        }
     }
}


pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'fastapi-helloworld:latest'
        ARTIFACTORY_URL = 'https://your-artifactory-url/artifactory'
        ARTIFACTORY_REPO = 'your-artifactory-repo'
        ARTIFACTORY_PATH = 'trivy_reports'
    }

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

        stage('SonarQube Analysis') {
            steps {
                script {
                    // Run SonarScanner as a Docker container
                    sh 'docker run --rm \
                        -e SONAR_HOST_URL=http://192.168.0.112:9000 \
                        -e SONAR_TOKEN=sqa_4f8b0a96db55ae925fa6c1f6f714b7cca33b12ae \
                        -v "$(pwd):/usr/src" \
                        sonarsource/sonar-scanner-cli \
                        -Dsonar.projectKey=fastapi-helloworld \
                        -Dsonar.sources=app'
                }
            }
        }
        stage('Trivy Scan') {
            steps {
                script {
                    // Run Trivy scan directly on the locally built Docker image
                    sh "trivy image --format json -o trivy_report.json fastapi-helloworld:latest"
                }
            }
        }

        stage('Build Docker image') {
            steps {
                script {
                    sh 'docker login -u praveenraj29 -p ******'
                    sh 'docker build -t praveenraj29/fastapi-helloworld:latest .'
                    sh 'docker push praveenraj29/fastapi-helloworld:latest'
                }
            }
        }
    }
}

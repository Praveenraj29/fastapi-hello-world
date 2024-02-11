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

        stage('SonarQube Analysis') {
            steps {
                script {
                    // Run SonarScanner as a Docker container
                    sh 'docker run --rm \
                        -e SONAR_HOST_URL=http://192.168.0.112:9000 \
                        -e SONAR_TOKEN=sqp_5349b09ee3507c39aec61d1cd28b0d5a6a03dcb2 \
                        -v "$(pwd):/usr/src" \
                        sonarsource/sonar-scanner-cli \
                        -Dsonar.projectKey=fastapi-helloworld \
                        -Dsonar.sources=app'
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image
                    sh 'docker login -u praveenraj29 -p 473Msp20*'
                    sh 'docker build -t your-docker-image:latest .'
                    sh 'docker push your-docker-image:latest'
                }
            }
        }
        stage('Trivy Scan') {
            steps {
                script {
                    sh 'docker login -u praveenraj29 -p 473Msp20*'
                    sh 'docker pull your-docker-image:latest'
                    sh 'docker save your-docker-image:latest | trivy image - --exit-code 1'
                }
            }
        }
    }
}

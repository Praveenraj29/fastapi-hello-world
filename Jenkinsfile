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
        stage('Trivy Scan') {
            steps {
                script {
                    // Build Docker image
                    sh 'docker build -t fastapi-helloworld:latest .'

                    // Run Trivy scan directly on the locally built Docker image
                    def trivyCommand = 'trivy image --output trivy_report.json --format json fastapi-helloworld:latest'
                    def trivyExitCode = sh(script: trivyCommand, returnStatus: true)

                    // If Trivy finds critical vulnerabilities, throw an error
                    if (trivyExitCode != 0) {
                        error "Trivy found critical vulnerabilities. Please review the report."
                    }

                    // Generate HTML report from JSON using trivy report command
                    sh 'trivy image --format json -o trivy_report.json fastapi-helloworld:latest'
                    // sh 'trivy report --input trivy_report.json --format html --output trivy_report.html'

                    // Archive both JSON and HTML reports for later reference
                    archiveArtifacts artifacts: ['trivy_report.json', 'trivy_report.html'], allowEmptyArchive: true
                }
            }
        }
        stage('Build Docker image') {
            steps {
                script {
                    sh 'docker login -u praveenraj29 -p 473Msp20*'
                    sh 'docker build -t praveenraj29/fastapi-helloworld:latest .'
                    sh 'docker push praveenraj29/fastapi-helloworld:latest'
                }
            }
        }
    }
}

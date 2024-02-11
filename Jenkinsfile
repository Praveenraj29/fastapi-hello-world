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
                    // Run Trivy scan directly on the locally built Docker image
                    sh "trivy image --format json -o trivy_report.json ${DOCKER_IMAGE}"

                    // Upload Trivy JSON report to Artifactory
                    sh "curl -u ${env.ARTIFACTORY_USERNAME}:${env.ARTIFACTORY_API_KEY} -T trivy_report.json ${env.ARTIFACTORY_URL}/${env.ARTIFACTORY_REPO}/${env.ARTIFACTORY_PATH}/"
                }
            }
        }

        stage('Convert JSON to HTML') {
            steps {
                script {
                    // Download Trivy JSON report from Artifactory
                    sh "curl -u ${env.ARTIFACTORY_USERNAME}:${env.ARTIFACTORY_API_KEY} -O ${env.ARTIFACTORY_URL}/${env.ARTIFACTORY_REPO}/${env.ARTIFACTORY_PATH}/trivy_report.json"

                    // Generate HTML report from JSON using trivy report command
                    sh 'trivy report --input trivy_report.json --format html --output trivy_report.html'

                    // Archive JSON report for later reference
                    archiveArtifacts artifacts: 'trivy_report.json', allowEmptyArchive: true

                    // Archive HTML report for later reference
                    archiveArtifacts artifacts: 'trivy_report.html', allowEmptyArchive: true
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

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

        // Add more stages as needed for your build and deployment process
        stage('Build') {
            steps {
                // Add build steps here
            }
        }

        stage('Deploy') {
            steps {
                // Add deployment steps here
            }
        }
    }

    post {
        always {
            // Clean up steps or post-build actions can go here
        }
    }
}

pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                // Pull the latest source code
                checkout scm
                
                // Build the Docker image
                script {
                    docker.build('tooba009/webapp1:latest')
                    docker.withRegistry('https://registry.hub.docker.com', 'tooba009') {
                        docker.image('tooba009/webapp1:latest').push()
                    }
                }
            }
        }

        stage('Test') {
            steps {
                // Implement necessary tests for your web application
                // For example, you can run a simple test command here
                sh 'echo "Running tests..."'
            }
        }

        stage('Deploy') {
            steps {
                // Pull the Docker image from Docker Hub
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'tooba009') {
                        docker.image('tooba009/webapp1:latest').pull()
                    }
                }

                // Deploy the application on a local Docker environment for testing
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'tooba009') {
                        docker.image('tooba009/webapp1:latest').run('-p 8080:80 -d --name webapp1-container')
                    }
                }
            }
        }
    }

    post {
        failure {
            // Rollback to the previous version in case of a failed deployment
            script {
                docker.withRegistry('https://registry.hub.docker.com', 'tooba009') {
                    docker.image('tooba009/webapp1:latest').remove()
                    docker.image('tooba009/webapp1:1.0').tag('tooba009/webapp1:latest', 'tooba009/webapp1:1.0')
                    docker.image('tooba009/webapp1:1.0').push()
                }
            }

            // Send email notifications to the team in case of a failed deployment
            emailext subject: 'Failed Deployment: WebApp1',
                      body: 'The deployment of WebApp1 has failed. Please check the Jenkins logs for details.',
                      to: 'sp20-bcs-009@cuiatk.edu.pk',
                      attachLog: true
        }
    }
}

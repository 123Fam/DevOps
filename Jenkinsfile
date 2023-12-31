pipeline {
    agent any

    environment {
       
        DOCKER_IMAGE = "fatimaali563/personal-portfolio"
        DOCKER_TAG = "${env.BUILD_ID}"
    }

    stages {
        stage('Build') {
            steps {
                script {
                    
                    dockerImage = docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                }
            }
        }

        stage('Push') {
            steps {
                script {
                  
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Test') {
            steps {
                
                sh 'ls -l index.html' 
            }
        }

        stage('Deploy') {
            steps {
                script {
                 
                    sshPublisher(
                        publishers: [
                            sshPublisherDesc(
                                transfers: [sshTransfer(
                                    execCommand: """
                                        docker pull ${DOCKER_IMAGE}:${DOCKER_TAG}
                                        docker stop personal-portfolio-container || true
                                        docker rm personal-portfolio-container || true
                                        docker run -d --name personal-portfolio-container -p 80:80 ${DOCKER_IMAGE}:${DOCKER_TAG}
                                    """
                                )]
                            )
                        ]
                    )
                }
            }
        }
    }

    post {
        failure {
            mail(
                to: 'sp20-bcs-002@cuiatk.edu.pk',
                subject: "Failed Pipeline: ${env.JOB_NAME} [${env.BUILD_NUMBER}]",
                body: "Something is wrong with the build ${env.BUILD_URL}"
            )
        }
    }
}

pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                script {
                    dockerImage = docker.build("fatimaali563/tooba-portfolio:${env.BUILD_ID}")
                }
            }
        }
        stage('Push') {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'docker-credentials') {
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
                                configName: "assignment4", 
                                transfers: [sshTransfer(
                                    execCommand: """
                                        docker pull fatimaali563/tooba-portfolio:${env.BUILD_ID}
                                        docker stop fatimaali563-portfolio-container || true
                                        docker rm tooba-portfolio-container || true
                                        docker run -d --name tooba-portfolio-container -p 80:80 fatimaali563/tooba-portfolio:${env.BUILD_ID}
                                    """
                                )]
                            )
                        ]
                    )

                    
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

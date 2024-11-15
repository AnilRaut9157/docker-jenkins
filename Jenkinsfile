pipeline {
    agent any

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }
    
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/AnilRaut9157/docker-jenkins.git'
            }
        }
        
        stage('Sonar Analysis') {
            steps {
                sh """
                    ${SCANNER_HOME}/bin/sonar-scanner \
                    -Dsonar.host.url=http://52.183.28.12:9000/ \
                    -Dsonar.login=squ_49063763beb708d26cc063dc507b55a194aa64bb \
                    -Dsonar.projectName=docker-jenkins \
                    -Dsonar.java.binaries=target \
                    -Dsonar.projectKey=docker-jenkins
                """
            }
        }
        
        stage('Docker Build and Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'f1bebc5e-9bd3-46b6-94b8-7917f77a3c70') {
                        sh """
                            docker build -t my-note-app .
                            docker tag my-note-app anilkumarraut9157/my-note-app:latest
                            docker push anilkumarraut9157/my-note-app:latest
                        """
                    }
                }
            }
        }

        stage('Deploy to Docker') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'f1bebc5e-9bd3-46b6-94b8-7917f77a3c70') {
                        def containerName = "my-note-app-${new Date().format('yyyyMMddHHmmss')}"
                        sh """
                            docker stop my-note-app || true
                            docker rm my-note-app || true
                            docker run -d --name ${containerName} -p 8000:8000 anilkumarraut9157/my-note-app:latest
                        """
                    }
                }
            }
        }

        stage("Docker Compose Deployment") {
            steps {
                echo "Deploying using Docker Compose"
                sh """
                    docker-compose down || echo "No containers to stop"
                    docker-compose up -d
                """
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed.'
        }
        success {
            echo 'Pipeline executed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check logs for errors.'
        }
    }
}

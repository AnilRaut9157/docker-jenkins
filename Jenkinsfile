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

        stage('OWASP Analysis') {
            steps {
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'DP'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
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
                    sh """
                        # Stop and remove any running container named 'my-note-app'
                        docker ps -q --filter "name=my-note-app" | xargs -r docker stop
                        docker ps -aq --filter "name=my-note-app" | xargs -r docker rm

                        # Run the new container
                        docker run -d --name my-note-app -p 8000:8000 anilkumarraut9157/my-note-app:latest
                    """
                }
            }
        }
    }

    
    }
}

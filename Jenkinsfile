pipeline {
    agent any

    stages {
        stage ("code") {
            steps {
                echo "Cloning the code"
                git url: "https://github.com/AnilRaut9157/docker-jenkins.git", branch: "main"
            }
        }
        stage ("build") {
            steps {
                echo "Building the Docker image"
                sh "docker build -t to-do-app ."
            }
        }
        stage ("push to docker hub") {
            steps {
                echo "Pushing the image to Docker Hub"
                withCredentials([usernamePassword(credentialsId: "dockerhub", passwordVariable: "dockerHubPass", usernameVariable: "dockerHubUser")]) {
                    sh "docker tag to-do-app ${env.dockerHubUser}/to-do-app:latest"
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "docker push ${env.dockerHubUser}/to-do-app:latest"
                }
            }
        }
        stage ("deploy") {
            steps {
                echo "Deploying using Docker Compose"
                sh "docker-compose down && docker-compose up -d"
            }
        }
    }
}

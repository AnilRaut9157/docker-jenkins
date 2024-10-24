pipeline {
    agent any

    stages {
        stage ("code"){
            steps {
                echo "cloning the code"
                git url: "https://github.com/AnilRaut9157/docker-jenkins.git", branch: "main"
            }
        }
        stage ("build"){
            steps {
                echo "building the image"
                sh "docker build -t its-my-note-app ."
            }
        }
        stage ("push to docker hub"){
            steps {
                echo "pushing the image to docker hub"
                withCredentials([usernamePassword(credentialsId:"dockerhub" , passwordVarible:"dockerHubPass", usernameVariable:"dockerHubUser")])
                sh "docker tag its-my-note-app ${env.dockerHubUser}/its-my-note-app:letest"
                sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                sh "docker push ${env.dockerHubUser}/its-my-note-app:latest"
            }
        }
        stage ("deploy"){
            steps {
                echo "this is deploy stage"
                sh "docker-compose down && docker-compose up -d"
            }
        }
    }
}

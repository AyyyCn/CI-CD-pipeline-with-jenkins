pipeline {
    agent any

    environment {
        IMAGE_NAME = "ayyycn/springboot-docker"
        VERSION = "latest"
    }

    stages {
        stage('Cloner le dépôt') {
            steps {
                git branch: 'main', url: 'https://github.com/AyyyCn/CI-CD-pipeline-with-jenkins.git'
            }
        }

        stage('Build Maven') {
            steps {
                sh './mvnw clean install -DskipTests'
            }
        }

        stage('Build de l’image Docker') {
            steps {
                script {
                    dockerImage = docker.build("${IMAGE_NAME}:${VERSION}")
                }
            }
        }

        stage('Push sur Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    script {
                        docker.withRegistry("https://index.docker.io/v1/", "dockerhub") {
                            dockerImage.push()
                        }
                    }
                }
            }
        }
    }
}

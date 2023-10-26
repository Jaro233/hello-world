pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-login') // Docker Hub credentials ID
        SSH_CREDENTIALS = credentials('webserver-creds') // SSH credentials ID
        IMAGE_NAME = 'j4ro123/hello-world:latest'
        TARGET_SERVER = credentials('webserver-ip')
    }

    stages {
        stage('Build Maven App') {
            steps {
                sh "mvn clean package"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build(env.IMAGE_NAME)
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('', env.DOCKER_HUB_CREDENTIALS) {
                        docker.image(env.IMAGE_NAME).push()
                    }
                }
            }
        }

        stage('Deploy to Target Server') {
            steps {
                script {
                    sshagent(credentials: [env.SSH_CREDENTIALS]) {
                        sh "ssh ubuntu@${env.TARGET_SERVER} 'docker pull ${env.IMAGE_NAME}'"
                        sh "ssh ubuntu@${env.TARGET_SERVER} 'docker run -d -p 8080:8080 ${env.IMAGE_NAME}'"
                    }
                }
            }
        }
    }
}

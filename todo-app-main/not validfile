pipeline {
    agent any
    environment {
        DOCKER_CRED = credentials('dockerhub-KABILESH77') // Update with your DockerHub credentials ID
    }

    stages {
        stage('Build image') {
            steps {
                sh 'docker build -t KABILESH77/todo-app:${BUILD_ID} .'  // Update with your DockerHub username
                sh 'docker images'
            }
        }

        stage('Push Docker hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-KABILESH77', usernameVariable: 'DOCKER_CRED_USR', passwordVariable: 'DOCKER_CRED_PSW')]) { 
                    sh 'docker login -u ${DOCKER_CRED_USR} -p ${DOCKER_CRED_PSW}' 
                    sh 'docker push KABILESH77/todo-app:${BUILD_ID}'  // Update with your DockerHub username
                }
            }
        }

        stage('Deploy to Server') {
            steps {
                script {
                    def gitClone = 'git clone https://github.com/KABILESH77/to-do-list-app.git'  // Update with your repo URL
                    def dockerPull = 'docker pull KABILESH77/todo-app'  // Update with your DockerHub image name
                    def dockerComposeDown = 'docker compose down --volumes'
                    def deleteImages = 'docker image prune -a --force'
                    def dockerComposeUp = 'docker compose up -d'
                   
                    sshagent(['VM-APP']) {  // Make sure 'VM-APP' is the correct Jenkins SSH credential ID
                        sh returnStatus: true, script: "ssh -o StrictHostKeyChecking=no your-username@your-server-ip ${gitClone} && ${dockerPull}"
                        sh returnStatus: true, script: "ssh -o StrictHostKeyChecking=no your-username@your-server-ip ${dockerComposeDown}"
                        sh returnStatus: true, script: "ssh -o StrictHostKeyChecking=no your-username@your-server-ip ${deleteImages}"
                        sh returnStatus: true, script: "ssh -o StrictHostKeyChecking=no your-username@your-server-ip cd todo-app && ${dockerComposeUp}"
                    }
                }
            }
        }
    }

    post {
        always {
            sh 'docker image prune -a --force'
        }
    }
}

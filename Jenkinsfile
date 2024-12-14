pipeline {
    agent any
    environment {
        DOCKER_CRED = credentials('dockerhub-mrmastermind77') // Updated DockerHub credentials ID
    }

    stages {
        stage('Build image') {
            steps {
                script {
                    // This should run inside a node context
                    sh 'docker build -t mrmastermind77/todo-app:${BUILD_ID} .' // Updated with your DockerHub username
                    sh 'docker images'
                }
            }
        }

        stage('Push Docker hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-mrmastermind77', usernameVariable: 'DOCKER_CRED_USR', passwordVariable: 'DOCKER_CRED_PSW')]) { 
                        sh 'docker login -u ${DOCKER_CRED_USR} -p ${DOCKER_CRED_PSW}' 
                        sh 'docker push mrmastermind77/todo-app:${BUILD_ID}' // Updated with your DockerHub username
                    }
                }
            }
        }

        stage('Deploy to Server') {
            steps {
                script {
                    def gitClone = 'git clone https://github.com/KABILESH77/to-do-list-app.git' // Updated with your GitHub repo URL
                    def dockerPull = 'docker pull mrmastermind77/todo-app:${BUILD_ID}' // Updated with your DockerHub image name
                    def dockerComposeDown = 'docker compose down --volumes'
                    def deleteImages = 'docker image prune -a --force'
                    def dockerComposeUp = 'docker compose up -d'

                    sshagent(['VM-APP-SSH']) { // Using your updated SSH credential ID
                        // Wrap this block in a node context to make the ssh step work
                        node {
                            sh """
                            ssh -o StrictHostKeyChecking=no your-username@your-server-ip "
                                ${gitClone} &&
                                cd to-do-list-app &&
                                ${dockerPull} &&
                                ${dockerComposeDown} &&
                                ${deleteImages} &&
                                ${dockerComposeUp}
                            "
                            """
                        }
                    }
                }
            }
        }
    }

    post {
        always {
            script {
                // Wrap the docker cleanup in a node context
                node {
                    sh 'docker image prune -a --force'  // Clean up any Docker images after the pipeline execution
                }
            }
        }
    }
}

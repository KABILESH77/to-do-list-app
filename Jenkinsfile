pipeline {
    agent any
    environment {
        DOCKER_CRED = credentials('dockerhub-mrmastermind77') // Updated DockerHub credentials ID
    }

    stages {
        stage('Build image') {
            steps {
                script {
                    // Use bat instead of sh for Windows
                    bat 'docker build -t mrmastermind77/todo-app:${BUILD_ID} .' // Updated with your DockerHub username
                    bat 'docker images'
                }
            }
        }

        stage('Push Docker hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-mrmastermind77', usernameVariable: 'DOCKER_CRED_USR', passwordVariable: 'DOCKER_CRED_PSW')]) { 
                        bat 'docker login -u %DOCKER_CRED_USR% -p %DOCKER_CRED_PSW%' // Use Windows environment variable syntax
                        bat 'docker push mrmastermind77/todo-app:${BUILD_ID}' // Updated with your DockerHub username
                    }
                }
            }
        }

        stage('Deploy to Server') {
            steps {
                script {
                    def gitClone = 'git clone https://github.com/KABILESH77/to-do-list-app.git' // Updated with your GitHub repo URL
                    def dockerPull = 'docker pull mrmastermind77/todo-app:${BUILD_ID}' // Updated with your DockerHub image name
                    def dockerComposeDown = 'docker-compose down --volumes' // Updated for Windows compatible command
                    def deleteImages = 'docker image prune -a --force'
                    def dockerComposeUp = 'docker-compose up -d'

                    sshagent(['VM-APP-SSH']) { // Using your updated SSH credential ID
                        // Wrap this block in a node context to make the ssh step work
                        node {
                            bat """
                            ssh -o StrictHostKeyChecking=no your-username@your-server-ip ^
                                ${gitClone} && ^
                                cd to-do-list-app && ^
                                ${dockerPull} && ^
                                ${dockerComposeDown} && ^
                                ${deleteImages} && ^
                                ${dockerComposeUp}
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
                    bat 'docker image prune -a --force'  // Clean up any Docker images after the pipeline execution
                }
            }
        }
    }
}

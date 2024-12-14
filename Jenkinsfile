pipeline {
    agent any
    environment {
        DOCKER_CRED = credentials('dockerhub-mrmastermind77') // Updated DockerHub credentials ID
    }

    stages {
        stage('Build image') {
            steps {
                script {
                    // This should run inside a node context, using bat for Windows
                    bat 'docker build -t mrmastermind77/todo-app:${BUILD_ID} .' // Updated with your DockerHub username
                    bat 'docker images' // Show Docker images for debugging
                }
            }
        }

        stage('Push Docker hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-mrmastermind77', usernameVariable: 'DOCKER_CRED_USR', passwordVariable: 'DOCKER_CRED_PSW')]) { 
                        bat "docker login -u ${DOCKER_CRED_USR} -p ${DOCKER_CRED_PSW}" // Docker login with credentials
                        bat "docker push mrmastermind77/todo-app:${BUILD_ID}" // Push to DockerHub
                    }
                }
            }
        }

        stage('Deploy to Server') {
            steps {
                script {
                    def gitClone = 'git clone https://github.com/KABILESH77/to-do-list-app.git' // Updated with your GitHub repo URL
                    def dockerPull = 'docker pull mrmastermind77/todo-app:${BUILD_ID}' // Updated with your DockerHub image name
                    def dockerComposeDown = 'docker-compose down --volumes' // Ensure Docker Compose is correct
                    def deleteImages = 'docker image prune -a --force' // Clean up unused Docker images
                    def dockerComposeUp = 'docker-compose up -d' // Deploy the Docker containers

                    sshagent(['VM-APP-SSH']) { // Using your updated SSH credential ID
                        // Wrap this block in a node context to make the ssh step work
                        node {
                            bat """
                            ssh -i /path/to/your/ssh/key -o StrictHostKeyChecking=no your-username@your-server-ip "
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
                // Wrap the Docker cleanup in a node context to ensure proper execution on Windows
                node {
                    bat 'docker image prune -a --force'  // Clean up any Docker images after the pipeline execution
                }
            }
        }
    }
}

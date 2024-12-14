pipeline {
    agent any
    environment {
                DOCKER_CRED = credentials('dockerhub-yunandar711')
            }
            
    stages {
        stage('Build image') {
            steps {
                    sh 'docker build -t yunandar711/todo-app:${BUILD_ID} .'
                    sh 'docker images'
                }
        }

        stage('Push Docker hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-yunandar711', usernameVariable: 'DOCKER_CRED_USR', passwordVariable: 'DOCKER_CRED_PSW')]) { 
                    sh 'docker login -u ${DOCKER_CRED_USR} -p ${DOCKER_CRED_PSW}' 
                    sh 'docker push yunandar711/todo-app:${BUILD_ID}' }
                }
        }

        stage('Deploy to Server') {
            steps {
                script {
                    def gitClone = 'git clone https://github.com/yunandarz/todo-app.git'
                    def dockerPull = 'docker pull yunandar711/todo-app'
                    def dockerComposeDown = 'docker compose down --volumes'
                    def deleteImages = 'docker image prune -a --force'
                    def dockerComposeUp = 'docker compose up -d'
                   
                    sshagent(['VM-APP']) {
                        sh returnStatus: true, script: "ssh -o StrictHostKeyChecking=no yunandar@103.174.115.41 ${gitClone} && ${dockerPull}"
                        sh returnStatus: true, script: "ssh -o StrictHostKeyChecking=no yunandar@103.174.115.41 ${dockerComposeDown}"
                        sh returnStatus: true, script: "ssh -o StrictHostKeyChecking=no yunandar@103.174.115.41 ${deleteImages}"
                        sh returnStatus: true, script: "ssh -o StrictHostKeyChecking=no yunandar@103.174.115.41 cd todo-app && ${dockerComposeUp}"
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
// Jenkinsfile
pipeline {
    agent any
    
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
        IMAGE_NAME = "erakash2000/my-node-app"
        DEPLOY_SERVER = "ubuntu@3.111.215.31"
    }
    
    stages {
        stage('Clone Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/akash-ahirwar02/jenkins-my-node-app.git'
                echo "Code clone ho gaya!"
            }
        }
        
        stage('Install & Test') {
            steps {
                sh 'npm install'
                sh 'npm test'
                echo "Tests pass ho gaye!"
            }
        }
        
        stage('Docker Build') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} ."
                sh "docker tag ${IMAGE_NAME}:${BUILD_NUMBER} ${IMAGE_NAME}:latest"
                echo "Docker image ban gayi!"
            }
        }
        
        stage('Push to DockerHub') {
            steps {
                sh "echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin"
                sh "docker push ${IMAGE_NAME}:${BUILD_NUMBER}"
                sh "docker push ${IMAGE_NAME}:latest"
                echo "DockerHub pe push ho gayi!"
            }
        }
        
        stage('Deploy') {
            steps {
                sshagent(['deploy-server-ssh']) {
                    sh """
                        ssh -o StrictHostKeyChecking=no ${DEPLOY_SERVER} '
                            docker pull ${IMAGE_NAME}:latest
                            docker stop my-app || true
                            docker rm my-app || true
                            docker run -d --name my-app -p 3000:3000 ${IMAGE_NAME}:latest
                            echo "App deploy ho gayi!"
                        '
                    """
                }
            }
        }
    }
    
    post {
        success { echo "Pipeline successful! App live hai." }
        failure { echo "Pipeline fail hua. Logs dekho." }
    }
}

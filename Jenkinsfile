pipeline {
        agent {
            label 'windows' 
        }

    tools{
        nodejs 'node'
    }

    environment {
        APP_NAME = "react-pipeline2"
        RELEASE = "1.0.0"
        DOCKER_USER = "ersonusaini"
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
        DOCKER_PASS = "docker-token"
        CONTAINER_NAME = "react-pipeline2"
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Git Checkout with SCM') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/sonusainiawsexpert/react-pipeline2.git']])
            }
        }

        stage('Build') {
            steps {
                bat 'npm install'
                bat 'npm run build'
            }
        }

        stage('test') {
            steps {
                sh 'npm run test'
            }
        }


        stage('Build and Push in Dockerhub') {
            steps {
                script{
                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image = docker.build "${IMAGE_NAME}"
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }           
                }
            }
        }

        stage('Deploy') {
            steps {
                bat "docker run -d -p 8085:80 --restart always --name ${CONTAINER_NAME} ${IMAGE_NAME}"
            }
        }
    }
}
pipeline {
    agent{
        label 'Jenkins-docker-agent'
    }

    environment {
        APP_NAME = "react-pipeline2"
        RELEASE = "1.0.0"
        DOCKER_USER = "hiteshsn785"
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
        DOCKER_PASS = "Docker-login"
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
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'github-login', url: 'https://github.com/hiteshsaini33/react-pipeline2']])
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
                sh 'npm run build'
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

        stage ('Remove docker container and images') {
            steps {
                script {
                    sh "docker stop ${CONTAINER_NAME}"
                    sh "docker rm ${CONTAINER_NAME}"
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker rmi ${IMAGE_NAME}:latest"
                }
            }
        }

        stage('Deploy') {
            steps {
                sh "docker run -d -p 8085:80 --restart always --name ${CONTAINER_NAME} ${IMAGE_NAME}"
            }
        }
    }
}
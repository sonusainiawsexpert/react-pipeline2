pipeline {
        agent {
            label 'windows' 
        }

    tools{
        nodejs 'node'
        jdk 'Java'
        
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
                checkout scmGit(branches: [[name: '*/main']], extensions: [])
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

        // stage("Sonarqube Analysis") {
        //     steps {
        //         script {
        //             def scannerHome = tool 'sonarqube-scanner';
        //             withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') {
        //                 sh "${tool("sonarqube-scanner")}/bin/sonar-scanner -Dsonar.projectKey=react-pipeline2 -Dsonar.projectName=react-pipeline2"
        //             }
        //         }
        //     }

        // }

        // stage("Quality Gate") {
        //     steps {
        //         script {
        //             waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-token'
        //         }
        //     }

        // }


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

        // stage ('Remove docker container and images') {
        //     steps {
        //         script {
        //             sh "docker stop ${CONTAINER_NAME}"
        //             sh "docker rm ${CONTAINER_NAME}"
        //             sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
        //             sh "docker rmi ${IMAGE_NAME}:latest"
        //         }
        //     }
        // }

        stage('Deploy') {
            steps {
                sh "docker run -d -p 8085:80 --restart always --name ${CONTAINER_NAME} ${IMAGE_NAME}"
            }
        }
    }

    //     post {
    //     failure {
    //         emailext body: '''${SCRIPT, template="groovy-html.template"}''', 
    //                 subject: "${env.JOB_NAME} - Build # ${env.BUILD_NUMBER} - Failed", 
    //                 mimeType: 'text/html',to: "sainihitesh33@gmail.com"
    //         }
    //      success {
    //            emailext body: '''${SCRIPT, template="groovy-html.template"}''', 
    //                 subject: "${env.JOB_NAME} - Build # ${env.BUILD_NUMBER} - Successful", 
    //                 mimeType: 'text/html',to: "sainihitesh33@gmail.com"
    //       }      
    // }
}
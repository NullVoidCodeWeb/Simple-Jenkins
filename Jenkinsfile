// pipeline{
//     agent any
//     stages{
//         stage("Checkout"){
//             steps{
//                 checkout scm
//             }
//         }

//         stage("Test"){
//             steps{
//                 sh 'sudo apt install npm'
//                 sh 'npm test'
//             }
//         }

//         stage("Build"){
//             steps{
//                 sh 'npm run build'
//             }
//         }
//     }
// }

// pipeline {
//     agent any
    
//     environment {
//         DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
//         DOCKER_IMAGE_NAME = "vedantchimote/simple-jenkins"
//         DOCKER_IMAGE_TAG = "${BUILD_NUMBER}"
//     }
    
//     stages {
//         stage("Checkout") {
//             steps {
//                 checkout scm
//             }
//         }

//         stage("Test") {
//             steps {
//                 sh 'sudo apt install npm'
//                 sh 'npm test'
//             }
//         }

//         stage("Build") {
//             steps {
//                 sh 'npm run build'
//             }
//         }
        
//         stage("Build Docker Image") {
//             steps {
//                 script {
//                     docker.build("${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}")
//                 }
//             }
//         }
        
//         stage("Push to DockerHub") {
//             steps {
//                 script {
//                     docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials') {
//                         docker.image("${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}").push()
//                         docker.image("${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}").push("latest")
//                     }
//                 }
//             }
//         }
//     }
    
//     post {
//         always {
//             sh 'docker logout'
//         }
//     }
// }


pipeline {
    agent any
    
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        DOCKER_IMAGE_NAME = "vedantchimote/simple-jenkins"
        DOCKER_IMAGE_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        stage("Checkout") {
            steps {
                checkout scm
            }
        }

        stage("Install Dependencies") {
            steps {
                sh 'npm install'
            }
        }

        stage("Test") {
            steps {
                script {
                    try {
                        sh 'npm test'
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        error("Test stage failed: ${e.message}")
                    }
                }
            }
        }

        stage("Build") {
            steps {
                script {
                    try {
                        sh 'npm run build'
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        error("Build stage failed: ${e.message}")
                    }
                }
            }
        }
        
        stage("Build Docker Image") {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}")
                }
            }
        }
        
        stage("Push to DockerHub") {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials') {
                        docker.image("${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}").push()
                        docker.image("${DOCKER_IMAGE_NAME}:${DOCKER_IMAGE_TAG}").push("latest")
                    }
                }
            }
        }
    }
    
    post {
        always {
            sh 'docker logout'
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}

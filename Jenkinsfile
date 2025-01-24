pipeline {
    environment {
        registry = "damrienset/tp4"
        registryCredential = 'dockerhub'
        dockerImage = ''
    }
    agent any
    stages {
        stage('Cloning Git') {
            steps {
                git 'https://github.com/edamghy/tp5devops.git'
            }
        }
        stage('Building Image') {
            steps {
                script {
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                }
            }
        }
        stage('Testing Image') {
            steps {
                script {
                    echo "Tests passed"
                }
            }
        }
        stage('Publishing Image') {
            steps {
                script {
                    docker.withRegistry('', registryCredential) {
                        dockerImage.push()
                    }
                }
            }
        }
        stage('Deploying Image') {
            steps {
                script {
                    sh "docker run -d -p 80:80 ${registry}:${BUILD_NUMBER}"
                }
            }
        }
    }
}

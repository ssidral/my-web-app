pipeline {
    agent any
 
    environment {
        DOCKER_IMAGE = "{shrinivassidral}/{my-web-app}"
        DOCKER_TAG = "latest"
    }
    
    stages {
        stage('Checkout') {
            steps {
            	script{
            	   env.GIT_CURL_VERBOSE = '1'
            	   env.GIT_TRACE = '1'
            	   checkout scm
            	   }
                git branch: 'master', url: '{https://github.com/ssidral/my-web-app.git}',
                credentialsId: 'git-token'
            }
        }
 
        stage('Build Maven Project') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }
 
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${DOCKER_TAG}")
                }
            }
        }
 
        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'shrinivassidral') {
                        docker.image("${DOCKER_IMAGE}:${DOCKER_TAG}").push()
                    }
                }
            }
        }
 
        stage('Deploy Docker Container') {
            steps {
                sh '''
                    docker stop webapp || true
                    docker rm webapp || true
                    docker run -d -p 8081:8081 --name webapp ${DOCKER_IMAGE}:${DOCKER_TAG}
                '''
            }
        }
 
        stage('Cleanup Workspace') {
            steps {
                sh 'rm -rf *'
            }
        }
    }
}

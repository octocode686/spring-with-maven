pipeline {
    agent {
        label 'my-ssh-slave'
    }
    tools {
        maven 'Maven'
        dockerTool 'docker'
    }
    environment {
        DOCKERHUB_CREDENTIALS = credentials('my_dockerhub_creds')
        IMAGE_NAME = 'octocode686/myspringapp'
    }
    triggers {
        githubPush()
    }
    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/octocode686/spring-with-maven.git'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test' //This is for testing the nodejs modules
            }
        }
        stage('Docker login'){
            steps{
                sh 'docker login -u $DOCKERHUB_CREDENTIALS_USR -p $DOCKERHUB_CREDENTIALS_PSW'
            }
        }
        stage('Docker build and tag'){
            steps{
                sh 'docker build -t ${IMAGE_NAME} -f Dockerfile .'
                sh 'docker tag ${IMAGE_NAME} ${IMAGE_NAME}:${BUILD_NUMBER}'
            }
        }
        stage('Docker Push'){
            steps{
                sh 'docker push ${IMAGE_NAME}:${BUILD_NUMBER}'
            }
        }
        stage('Deploy') {
           steps {
                sh 'docker run -d -p 7000:8080 ${IMAGE_NAME}:${BUILD_NUMBER}'
           }
        }
        stage('Clean') {
            steps {
                cleanWs()
            }
        }
    }
}
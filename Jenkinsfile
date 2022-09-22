pipeline {
    agent {label "docker"}
    environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub')
    }
    stages { 
        stage('SCM Checkout') {
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/html']], extensions: [], userRemoteConfigs: [[credentialsId: 'github', url: 'git@github.com:InZeinly/somehtml.git']]])
            }
        }

        stage('Build docker image') {
            steps {  
                sh 'docker build -t inzein/htmlimage:latest .'
            }
        }
        stage('login to dockerhub') {
            steps{
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        stage('push image') {
            steps{
                sh 'docker push inzein/htmlimage:latest'
            }
        }
        stage('Connect to deploy') {
            steps{
                script {
                    sh """
                    #!/bin/bash
                    ssh -T -o StrictHostKeyChecking=no -i ~/aws-terraform.pem ubuntu@ec2-3-73-78-110.eu-central-1.compute.amazonaws.com' << EOF
                    docker run --rm -p 8888:80 inzein/htmlimage:latest
                    exit 0
                    << EOF
                    """
                    }
                }    
            }
        stage("Run on Deploy server") {
            steps{
                //sh 'docker pull inzein/htmlimage:47'
                sh 'docker run --rm -p 8888:80 inzein/htmlimage:latest'
                }
            }
        }   
post {
        always {
            sh 'docker logout'
        }
    }
}

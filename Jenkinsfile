#!/usr/bin/env groovy
@Library('aws-shared-library')_

pipeline {
    agent any
    
    tools {
        maven 'maven-3.9'
    }

    environment {
        IMAGE_NAME = 'amalkoc/twn-demo-app:java-maven-3.0'
    }

    stages {
        stage("Build App") {
            steps {
                script {
                    buildJar()
                }
            }
        }
        stage("Build Docker Image") {
            steps {
                script {
                    echo "Building the Docker Image"
                    buildImage(env.IMAGE_NAME)
                    dockerLogin()
                    dockerPush(env.IMAGE_NAME)
                }
            }
        }
        stage("Deploy") {
            steps {
                script {
                    echo "Deploying Docker Image to EC2 instance"
                    def dockerCmd = 'docker run -d -p 8080:8080 --name my-web-app amalkoc/twn-demo-app:aws-web-app-1.0'
                    sshagent(['ec2-server-key']) {
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@54.93.231.146 ${dockerCmd}"
                    }
                }
            }
        }
    }
}
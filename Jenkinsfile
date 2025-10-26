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
                    def dockerComposeCmd = "docker-compose -f docker-compose.yaml up -d"
                    sshagent(['ec2-server-key']) {
                        sh "scp docker-compose.yaml ec2-user@54.93.231.146:/home/ec2-user"
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@54.93.231.146 ${dockerComposeCmd}"
                    }
                }
            }
        }
    }
}
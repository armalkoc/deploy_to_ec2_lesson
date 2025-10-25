#!/usr/bin/env groovy
library identifier: 'aws-shared-library@main', retriever: modernSCM(
    [$class: GitSCMSource,
    remote:'https://github.com/armalkoc/aws-shared-library.git',
    credentialsId: 'github-am']
)

pipeline {
    agent any
    
    tools {
        maven 'maven-3.9'
    }

    evnironment {
        IMAGE_NAME = 'amalkoc/twn-demo-app:aws-web-app-1.0'
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
                    buildDockerImage(env.IMAGE_NAME)
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
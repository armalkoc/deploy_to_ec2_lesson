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
                    buildDockerImage() ''
                }
            }
        }
        stage("Deploy") {
            steps {
                script {
                    echo "Deploying Docker Image to EC2 instance"

                }
            }
        }
    }
}
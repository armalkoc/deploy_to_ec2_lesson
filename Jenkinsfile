#!/usr/bin/env groovy

pipeline {

    agent any

    stages {
        stage("Test") {
            steps {
                script {
                    echo "Testing the Application ..."
                }
            }
        }
        stage("Build") {
            steps {
                script {
                    echo "Building the Application ..."
                }
            }
        }
        stage("Deploy") {
            steps {
                script {
                    def dockerCmd = 'docker run -d -p 3080:3080 --name my-web-app amalkoc/twn-demo-app:aws-web-app-1.0'
                    sshagent(['ec2-server-key']) {
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@54.93.231.146 ${dockerCmd}"
                    }
                }
            }
        }
    }
}
#!/usr/bin/env groovy
@Library('aws-shared-library')_

pipeline {
    agent any
    
    tools {
        maven 'maven-3.9'
    }

    environment {
        repository = 'amalkoc/twn-demo-app'
 //       IMAGE_NAME = 'amalkoc/twn-demo-app:java-maven-5.0'
    }

    stages {
        stage("Incrementing App Version") {
            steps {
                script {
                    echo "Incrementing App Version"
                    sh "mvn build-helper:parse-version versions:set \
                        -DnewVersion=\\\${parsedVersion.majorVersion}.\\\${parsedVersion.minorVersion}.\\\${parsedVersion.nextIncrementalVersion} \
                        versions:commit"
                    
                    def matcher = readFile('pom.xml') =~ '<version>(.+)</version>'
                    def version = matcher[0][1]
                    env.IMAGE_NAME = "$repository\:$version-$BUILD_NUMBER"
                }
            }
        }
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
                    def shellCmd = "bash ./server-cmds.sh ${IMAGE_NAME}"
                    def ec2Instance = "ec2-user@54.93.231.146"
                    sshagent(['ec2-server-key']) {
                        sh "scp server-cmds.sh ${ec2Instance}:/home/ec2-user"
                        sh "scp docker-compose.yaml ${ec2Instance}:/home/ec2-user"
                        sh "ssh -o StrictHostKeyChecking=no ${ec2Instance} ${shellCmd}"
                    }
                }
            }
        }
        stage("Commit App Version Update") {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'github-armalkoc', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                        sh 'git remote set-url origin https://$USER:$PASS@github.com/armalkoc/deploy_to_ec2_lesson.git'
                        sh 'git add .'
                        sh 'git commit -m "ci: version bump"'
                        sh 'git push origin HEAD:am-jenkins-jobs'
                    }
                }
            }
        }
    }
}
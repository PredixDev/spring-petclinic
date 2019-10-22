#!/usr/bin/env groovy


pipeline {
    agent none
    environment {
        COMPLIANCEENABLED = true
    }
    options {
        skipDefaultCheckout()
        buildDiscarder(logRotator(artifactDaysToKeepStr: '1', artifactNumToKeepStr: '1', daysToKeepStr: '5', numToKeepStr: '10'))
    }
    stages {
        stage('Build') {
            agent {
                docker {
                    image 'maven:3.5'
                    label 'dind'
                    args '-v /root/.m2:/root/.m2'
                }
            }
            steps {
                sh 'rm -rf *'
                checkout scm
                script {
                    env.gitCommit = sh(returnStdout: true, script: 'git rev-parse HEAD').trim()
                    echo "Commit ID: ${gitCommit}"
                }
                sh 'MAVEN_OPTS="-Dorg.slf4j.simpleLogger.showDateTime=true -Dorg.slf4j.simpleLogger.dateTimeFormat=HH:mm:ss.SSS" mvn -B -s settings.xml -DskipTests clean package'
            }
            post {
                always {
                    deleteDir()
                }
                success {
                    echo "Build stage completed"
                }
                failure {
                    echo "Build stage failed"
                }
            }
        }

    }
    post {
        success {
            echo 'Your predix-cicd-java pipeline has completed.'
        }
        failure {
            echo "There was an error in the predix-cdcd-java pipeline build"
        }
    }
}


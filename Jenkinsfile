#!/usr/bin/env groovy

def templatePath = 'https://raw.githubusercontent.com/DerBrecher/Simple-JS/master/template.json' 
def templateName = 'frontend'

pipeline {
    agent any

    options {
        timeout(time: 20, unit: 'MINUTES') 
    }

    stages {
        stage('Preample'){
            steps{
                script {
                    openshift.withCluster() {
                        openshift.withProject() {
                            echo "Using project: ${openshift.project()}"
                            echo "On ClusterURL: ${openshift.cluster()}"
                        }
                    }
                }
            }
        }
        stage('cleanup') {
            steps {
                script {
                    openshift.withCluster() {
                        openshift.withProject() {
                            echo 'Cleaning up'
                            openshift.selector("all", [ template : templateName ]).delete() 
                            if (openshift.selector("secrets", templateName).exists()) { 
                               openshift.selector("secrets", templateName).delete()                            
                            }
                            sh 'ls'
                        }
                    }
                }
            }
        }
        stage('create') {
            steps {
                script {
                    openshift.withCluster() {
                        openshift.withProject() {
                            // openshift.newApp(templatePath) 
                        }
                    }
                }
            }
        }

        stage('Build NodeJS') {
            steps {
                echo 'Building NodeJS..'
                script {
                    openshift.withCluster() {
                        openshift.withProject() {
                            sh 'rm -rf target'
                            sh 'mkdir target'
                            sh 'cp -rf scripts/ target/'
                            sh 'cp index.html target/index.html'
                        }
                    }
                }
            }
        }

        stage('Build Docker Container') {
            steps {
                echo 'Building Docker Container..'
                script {
                    openshift.withCluster() {
                        openshift.withProject() {
                            def app = docker.build("frontend-httpd")
                        }
                    }
                }
            }
        }

        stage('Test') {
            steps {
                echo 'Testing..'
                sh 'pwd'
                sh 'oc whoami'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}
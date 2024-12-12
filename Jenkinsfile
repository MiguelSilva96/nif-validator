#!groovy
/*
* 20241212 Miguel Silva
* CI/CD on NIF validator
*/

/* groovylint-disable-next-line CompileStatic */
pipeline {
    agent any
    environment {
        HOME = "${env.WORKSPACE}"
    }
    stages {
        stage('Create a docker environment') {
            agent {
                docker {
                    image 'python:3.11-slim'
                    reuseNode true
                }
            }
            steps {
                sh"""
                export PIP_DISABLE_PIP_VERSION_CHECK=1
                export PYTHONDONTWRITEBYTECODE=1
                export PYTHONUNBUFFERED=1
                export PATH="$HOME/.local/bin:${PATH}"
                # Install python libraries
                pip install --user -r requirements.txt
                pip install --user -r requirements-test.txt
                """
            }
        }

        stage('Unit tests') {
            agent {
                docker {
                    image 'python:3.11-slim'
                    reuseNode true
                }
            }
            steps {
                sh 'python3 -m pytest --junitxml results.xml tests/'
            }
            post {
                always {
                    archiveArtifacts artifacts: 'results.xml', fingerprint: true
                    junit 'results.xml'
                }
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying the application...'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Please check the logs.'
        }
    }
}
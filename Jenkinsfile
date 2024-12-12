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

        stage('Coverage report') {
            agent {
                docker {
                    image 'python:3.11-slim'
                    reuseNode true
                }
            }
            steps {
                sh'''
                python3 -m coverage run --source=. --omit=tests/* -m pytest tests
                python3 -m coverage report -m
                python3 -m coverage html
                '''
            }
            post {
                always {
                    publishHTML(target:[
                        allowMissing: false,
                        alwaysLinkToLastBuild: false,
                        keepAll: true,
                        reportDir: 'htmlcov',
                        reportFiles: 'index.html',
                        reportName: 'Coverage Report'
                    ])
                }
            }
        }
        stage('Quality analysis') {
            parallel {
                stage('Integration tests') {
                    agent any
                    steps {
                        echo "TODO: integration tests"
                    }
                }

                stage('User Interface tests') { 
                    agent any
                    steps {
                        echo "TODO: user interface tests"
                    }
                }
                stage('PEP8 verification') { 
                    agent {
                        docker {
                            image 'python:3.11-slim'
                            reuseNode true
                        }
                    }
                    steps {
                        sh 'python3 -m flake8 . --exclude site-packages --exit-zero'
                    }
                }
                stage('Cyclomatic complexity analysis') { 
                    agent {
                        docker {
                            image 'python:3.11-slim'
                            reuseNode true
                        }
                    }
                    steps {
                        sh 'python3 -m radon cc . -a -s --exclude site-packages'
                    }
                }
                
            }
        }

        stage('Deploy') {
            steps {
                withCredentials([usernamePassword(credentialsId:'dockerHub',paswordVariable='password', usernameVariable='username')]) {
                    sh"""
                    docker build -t ${username}/nif-validator .
                    docker login -u ${username} -p ${password}
                    docker push ${username}/nif-validator
                    """
                }
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
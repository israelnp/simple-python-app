pipeline {
 
    agent any
 
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/israelnp/simple-python-app.git'
            }
        }
 
        stage('Prepare') {
            steps {
               
                   script {
                    // Cria um ambiente virtual e ativa
                    sh 'python3 -m venv venv'
                    sh '. venv/bin/activate'
                    
                    // Usa pip3 dentro do ambiente virtual
                    sh 'venv/bin/pip3 install -r requirements.txt'
                }
                
            }
        }
 
        stage('Test') {
            steps {
                script {
                    // Cria um ambiente virtual e ativa
                    sh 'python3 -m venv venv'
                    sh '. venv/bin/activate'
                    sh 'venv/bin/pip3 install -r requirements.txt'
                    sh 'venv/bin/pytest testRoutes.py'
                }
               
            }
        }
 
        stage('SAST') {
            steps {
                sh '/usr/local/bin/horusec start -p .'
            }
        }
 
        stage('SCA') {
            steps {
                dependencyCheck additionalArguments: '', odcInstallation: 'OWASP-Dependency-Scan'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
 
        stage('Build') {
            steps {
                script {
                    sh 'docker build -t username/docker-image-name .'
                }
            }
        }
 
        stage('Deploy') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'dhpass', usernameVariable: 'dhuser')]) {
                        sh 'docker login -u ${dhuser} -p ${dhpass}'
                        sh 'docker push rupokify/python-jenkins-testone'
                    }
                }
            }
        }
 
        stage('DAST') {
            steps {
                script {
                    sh 'trivy image username/docker-image-name:latest'
                }
            }
        }
 
    }
 
}
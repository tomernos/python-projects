pipeline {
    agent any

    environment {
        GIT_CREDENTIALS = '12341234' 
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    git branch: 'main', credentialsId: env.GIT_CREDENTIALS, url: 'https://github.com/tomernos/python-projects.git'
                }
            }
        }

        stage('Setup') {
            steps {
                script {
                    sh '''
                    if ! command -v docker &> /dev/null
                    then
                        curl -fsSL https://get.docker.com/ | sh
                    fi
                    '''      
        
                    sh '''
                    if ! command -v docker-compose &> /dev/null
                    then
                        curl -L "https://github.com/docker/compose/releases/download/v2.20.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
                        chmod +x /usr/local/bin/docker-compose
                    fi
                    ''' 
            
                    sh '''
                    if ! command -v python3 &> /dev/null
                    then
                        sudo apt-get update
                        sudo apt-get install -y python3 python3-pip
                    fi
                    '''
                    
                    sh '''
                    if ! command -v git &> /dev/null
                    then
                        sudo apt-get update
                        sudo apt-get install -y git
                    fi
                    '''

                }
            }
        }

        stage('Build') {
            steps {
                script {
                    sh 'docker-compose build'
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    sh '''
                    source venv/bin/activate
                    pytest test_myflask.py
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh 'docker-compose up -d'
                }
            }
        }
    }

    post {
        always {
            sh 'docker-compose down'
        }
    }
}
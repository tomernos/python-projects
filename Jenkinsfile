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
                    if ! sudo command -v docker &> /dev/null
                    then
                        sudo curl -fsSL https://get.docker.com/ | sh
                    fi
                    '''
      
        
                    sh '''
                    if ! sudo command -v docker-compose &> /dev/null
                    then
                        sudo curl -L "https://github.com/docker/compose/releases/download/v2.20.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
                        sudo chmod +x /usr/local/bin/docker-compose
                    fi
                    ''' 
            
                    sh '''
                    if ! sudo command -v python3 &> /dev/null
                    then
                        sudo apt-get update
                        sudo apt-get install -y python3 python3-pip
                    fi
                    '''
                    sh '''
                    if ! sudo command -v git &> /dev/null
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
                    sh 'sudo docker-compose build'
                }
            }
        }

        stage('Check Environment') {
            steps {
                sh '''
                   echo "User: $(whoami)"
                   echo "Python Version:"
                   python3 --version
                   echo "Python Path:"
                   which python3
                   echo "Pip List:"
                   pip list
                   echo "Environment Variables:"
                   env
                '''
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    sh '''
                    pip3 install -r requirements.txt
                    python3 -m venv venv
                    source venv/bin/activate
                    pip3 install --upgrade pip
                    pip3 install -r requirements.txt
                    sleep 30
                    echo "Python version:"
                    python3 --version
                    echo "Pip version:"
                    pip3 --version
                    echo "Installed packages:"
                    pip3 list
                    python3 test_myflask.py
                    deactivate
                    '''
                

                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh 'sudo docker-compose up -d'
                }
            }
        }
    }

    post {
        always {
            sh 'sudo docker-compose down'
        }
    }
}
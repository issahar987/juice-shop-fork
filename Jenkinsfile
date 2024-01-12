pipeline {
    agent any

    triggers {
        // Trigger the pipeline on every push to the repository
        githubPush()
    }

    environment {
        REMOTE_HOST = '34.76.3.235'
        REMOTE_USER = 'issah'
        REMOTE_PATH = '/home/issah/juiceshop'
        SSH_CREDENTIALS_ID = 'ssh-key-to-remote-server'
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout your source code repository (assuming it contains the Juice Shop code)
                git 'https://github.com/issahar987/juice-shop-fork'
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Install Node.js and npm
                    sh '''
                        curl -sL https://deb.nodesource.com/setup_${NODEJS_VERSION}.x | bash -
                        apt-get install -y nodejs
                        node -v
                        npm -v
                        npm install
                    '''
                }
            }
        }

        stage('Deploy to Remote Server') {
            steps {
                // Deploy Juice Shop to remote server
                script {
                    withCredentials([sshUserPrivateKey(credentialsId: SSH_CREDENTIALS_ID, keyFileVariable: 'SSH_KEY')]) {
                        sh '''
                            scp -o StrictHostKeyChecking=no -i $SSH_KEY -r ./* ${REMOTE_USER}@${REMOTE_HOST}:${REMOTE_PATH}
                        '''
                    }
                }
            }
        }

        stage('Run Juice Shop on Remote Server') {
            steps {
                // Connect to remote server and start Juice Shop
                script {
                    withCredentials([sshUserPrivateKey(credentialsId: SSH_CREDENTIALS_ID, keyFileVariable: 'SSH_KEY')]) {
                        sh '''
                            ssh -o StrictHostKeyChecking=no -i $SSH_KEY ${REMOTE_USER}@${REMOTE_HOST} "cd ${REMOTE_PATH} && npm start &"
                            sleep 30
                        '''
                    }
                }
            }
        }

        stage('Run Tests') {
            steps {
                // Add your test commands here (e.g., using a testing framework like Cypress)
                // For simplicity, let's assume there's a script called 'run-tests.sh'
                script {
                    withCredentials([sshUserPrivateKey(credentialsId: SSH_CREDENTIALS_ID, keyFileVariable: 'SSH_KEY')]) {
                        sh '''
                            ssh -o StrictHostKeyChecking=no -i $SSH_KEY ${REMOTE_USER}@${REMOTE_HOST} "cd ${REMOTE_PATH} && ./run-tests.sh"
                        '''
                    }
                }
            }
        }
    }

    post {
        success {
            // Clean up (stop Juice Shop on remote server) only if the build was successful
            script {
                withCredentials([sshUserPrivateKey(credentialsId: SSH_CREDENTIALS_ID, keyFileVariable: 'SSH_KEY')]) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no -i $SSH_KEY ${REMOTE_USER}@${REMOTE_HOST} "cd ${REMOTE_PATH} && npm stop"
                    '''
                }
            }
        }
        failure {
            echo "Build failed! Skipping clean up."
        }
    }
}

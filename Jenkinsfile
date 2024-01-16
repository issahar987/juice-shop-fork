pipeline {
    agent any

    triggers {
        // Trigger the pipeline on every push to the repository
        githubPush()
    }

    environment {
        REMOTE_HOST = '35.233.110.153'
        REMOTE_USER = 'jenkins'
        REMOTE_PATH = '/home/jenkins/juice-shop-fork'
        SSH_CREDENTIALS_ID = 'ssh-key-to-remote-server'
    }

    stages {
        stage('Check User') {
            steps {
                script {
                    echo "Current user: ${env.USER}"
                    echo "whoami"
                }
            }
        }
        stage('Checkout') {
            steps {
                // Checkout your source code repository (assuming it contains the Juice Shop code)
                git 'https://github.com/issahar987/juice-shop-fork'
            }
        }

        // stage('SSH Connection') {
        //     steps {
        //         script {
        //             // The credentialsId is the ID of the SSH credentials you configured in Jenkins
        //             sshagent(credentials: [SSH_CREDENTIALS_ID]) {
        //                 // Now you can execute commands over SSH here
        //                 sh 'ssh ${REMOTE_USER}@${REMOTE_HOST} "ls"'
        //             }
        //         }
        //     }
        // }
        
        stage('Deploy to Remote Server') {
            steps {
                // Deploy Juice Shop to remote server
                script {
                    // The credentialsId is the ID of the SSH credentials you configured in Jenkins
                    node('webapp-agent') {
                        dir("${REMOTE_PATH}") {
                             sh 'git pull'
                         }
                    }
                }
            }
        }
        
        // stage('Build Docker Image on Remote Server') {
        //     steps {
        //         script {
        //             sshagent(credentials: [SSH_CREDENTIALS_ID]) {
        //                 sh "ssh ${REMOTE_USER}@${REMOTE_HOST} 'docker build -t ${DOCKER_IMAGE_NAME} -f ${REMOTE_PATH}/Dockerfile ${REMOTE_PATH}'"
        //             }
        //         }
        //     }
        // }

        // stage('Install dependecies') {
        //     steps {
                
        //         // Deploy Juice Shop to remote server
        //         script {
        //             // The credentialsId is the ID of the SSH credentials you configured in Jenkins
        //             sshagent(credentials: [SSH_CREDENTIALS_ID]) {
        //                 sh '''
        //                     ssh ${REMOTE_USER}@${REMOTE_HOST} "export PATH=$PATH:/home/issah/.nvm/versions/node/v20.11.0/bin/ && cd ${REMOTE_PATH} && npm install"
        //                 '''
        //             }
        //         }
        //     }
        // }

        // stage('Install dependecies') {
        //     steps {
        //         script {
        //             node('webapp-agent') {
        //                 // Komendy SSH bez użycia sshagent
        //                 env.PATH = "/home/jenkins/.nvm/versions/node/v20.11.0/bin:${env.PATH}"
        //                 dir("${REMOTE_PATH}") {
        //                     sh 'npm install'
        //                 }
                       
        //             }
        //         }
        //     }
        // }

        // stage('Run Juice Shop on Remote Server') {
        //     steps {
        //         // Connect to remote server and start Juice Shop
        //         script {
        //                 node('webapp-agent') {
        //                     env.PATH = "/home/jenkins/.nvm/versions/node/v20.11.0/bin:${env.PATH}"
        //                     dir("${REMOTE_PATH}") {
        //                             sh 'npm run start5000'
        //                     }
        //                 }
        //         }
        //     }
        // }

        stage('Run Juice Shop on Remote Server') {
            steps {
                // Connect to remote server and start Juice Shop
                script {
                    sshagent(credentials: [SSH_CREDENTIALS_ID]) {
                        sh '''
                            ssh -o StrictHostKeyChecking=no -i $SSH_KEY ${REMOTE_USER}@${REMOTE_HOST} "cd ${REMOTE_PATH} && npm run start5000 &"
                            sleep 30
                        '''
                    }
                }
            }
        }

    //     stage('Run Tests') {
    //         steps {
    //             // Add your test commands here (e.g., using a testing framework like Cypress)
    //             // For simplicity, let's assume there's a script called 'run-tests.sh'
    //             script {
    //                 withCredentials([sshUserPrivateKey(credentialsId: SSH_CREDENTIALS_ID, keyFileVariable: 'SSH_KEY')]) {
    //                     sh '''
    //                         ssh -o StrictHostKeyChecking=no -i $SSH_KEY ${REMOTE_USER}@${REMOTE_HOST} "cd ${REMOTE_PATH} && ./run-tests.sh"
    //                     '''
    //                 }
    //             }
    //         }
    //     }

    // post {
    //     success {
    //         // Clean up (stop Juice Shop on remote server) only if the build was successful
    //         script {
    //             withCredentials([sshUserPrivateKey(credentialsId: SSH_CREDENTIALS_ID, keyFileVariable: 'SSH_KEY')]) {
    //                 sh '''
    //                     ssh -o StrictHostKeyChecking=no -i $SSH_KEY ${REMOTE_USER}@${REMOTE_HOST} "cd ${REMOTE_PATH} && npm stop"
    //                 '''
    //             }
    //         }
    //     }
    //     failure {
    //         echo "Build failed! Skipping clean up."
    //     }
    // }
    }
}

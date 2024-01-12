pipeline {
    agent any

    environment {
        REMOTE_HOST = '34.76.3.235'
        REMOTE_USER = 'issah'
    }

    stages {
        stage('Build') {
            steps {
                echo 'Hello, Jenkins!'
            }
        }
        stage('Deploy to remote') {
            steps {
                script {
                    try {
                        sshagent([credentials('ssh-key-to-remote-server')]) {
                            sh "ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${REMOTE_HOST} ls"
                        }
                    } catch (Exception e) {
                        echo "Błąd SSH: ${e}"
                        currentBuild.result = 'FAILURE'
                    }
                }
            }
        }
    }
}

pipeline {
    agent any

    stages {
        stage('Hello') {
            steps {
                echo 'Hello World'
                sh 'echo "Another hello"'
                sh 'whoami'
            }
        }
        stage('Without docker') {
            steps {
                sh '''
                echo "No docker hello"
                touch gg1.txt
                '''
            }
        }
        stage('With docker') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                echo "No docker hello"
                touch gg2.txt
                npm --version
                '''
            }
        }
    }
}

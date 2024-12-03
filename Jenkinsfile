pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:20-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    ls -la
                    node --version
                    npm --version
                    npm ci --verbose
                    npm run build
                    ls -la
                '''
            }
        }
        stage('Test'){
            agent {
                docker {
                    image 'node:20-alpine'
                    reuseNode true
                }
            }
            steps{
                sh '''
                    test -f build/index.html
                    npm test
                '''
            }
        }
    }
}
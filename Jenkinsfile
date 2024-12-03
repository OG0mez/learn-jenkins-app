pipeline {
    agent any

    stages {
        stage('check-build'){
            steps{
                script{
                    if(fileExists('build/index.html')){
                        echo 'build exists, skipping build step'
                        skipBuild = true
                    }else{
                        echo 'build doesnt exist,running build step'
                        skipBuild = false
                    }
                }
            }
        }
        stage('Build') {
            //condition to skip the build step
            when{
                expression {!skipBuild}
            }
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
                    npm ci 
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
    post {
            always{
                junit 'test-results/junit.xml'
            } 
        }
}
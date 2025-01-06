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
        stage('Tests') {
            parallel {
                stage('Unit tests') {
                    agent {
                        docker {
                            image 'node:20-alpine'
                            reuseNode true
                        }
                    }

                    steps {
                        sh '''
                            #test -f build/index.html
                            npm test
                        '''
                    }
                    
                }

                stage('E2E') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                            reuseNode true
                        }
                    }

                    steps {
                        sh '''
                            npm install serve
                            node_modules/.bin/serve -s build &
                            sleep 10
                            npx playwright test  --reporter=html
                        '''
                    }

                    
                }
            }
        }
        stage('Deploy'){
           agent {
                docker {
                    image 'node:20-alpine'
                    reuseNode true
                }
            }
            steps{
                sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                '''
            } 
        }
    }
}
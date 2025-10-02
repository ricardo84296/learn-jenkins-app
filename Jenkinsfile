pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                //deleteDir()
                sh '''                    
                    ls -la                    
                    node --version
                    npm --version
                    npm ci --unsafe-perm
                    npm run build
                    ls -la
               '''
            }
        }

        stage('Tests') {
            parallel {
            
                stage('Unit Test') {
                agent {
                    docker {
                        image 'node:18-alpine'
                        reuseNode true
                    }
                }    

                steps {
                    //echo 'Test stage'
                    sh '''
                        test -f build/index.html
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
                        npx playwright test --reporter=html                    
                    '''
                }
            }

            }
        }

        
        
    }

    post {
        always {
            junit 'jest-results/junit.xml'
            //cleanWs() 
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright: HTML Report', reportTitles: '', useWrapperFileDirectly: true])
        }
    }
}
pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = 'ccc78abf-1937-4e95-bfee-b62b7c37b625'
    }

    stages {
        stage('Build') {
            agent {
                docker {
                    // some node js simple image
                    image 'node:18-alpine'
                    // shares workspace
                    reuseNode true
                }
            }
            steps {
                sh '''
                    ls -la
                    node --version
                    npm --version
                    #  "npm clean-install" installs exact dependencies from package-lock.json file
                    npm ci
                    # creates a build directory with a production build of your app
                    npm run build
                    ls -la
                '''
            }
        }

        stage('Tests') {
            parallel {
                stage('Unit Tests') {
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                        }  
                    }

                    steps {
                        sh '''
                            echo "Test stage"
                            # -f ensures it exists and is a regular file
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
                            # locally serves, instead of global, -s single-page app, & runs in the background so that terminal is freed up
                            node_modules/.bin/serve -s build &
                            sleep 10
                            npx playwright test --reporter=html
                        '''
                    }
                    
                }
            }
        }

        stage('Deploy') {
            agent {
                docker {
                    // some node js simple image
                    image 'node:18-alpine'
                    // shares workspace
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                    echo "Deploying to production. Site ID: $NETLIFY_SITE_ID"
                '''
            }
        }

        
    }

    post {
        always {
            junit 'jest-results/junit.xml'
            // made from the pipeline syntax module
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
        }
    }
}

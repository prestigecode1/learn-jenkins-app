pipeline {
    agent any

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

        stage('Test') {
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
                    npx playwright test
            '''
            }
            
        }
    }

    post {
        always {
            junit 'jest-results/junit.xml'
        }
    }
}

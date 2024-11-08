pipeline {
    agent any

    stages {
        stage('w/o docker') {
            steps {
                sh ''' 
                 echo "Without Docker"
                 ls -la
                '''
            }
        }
        stage('with docker') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
                
            }
            steps {
                sh '''
                    echo "With Docker"
                    npm --version
                    ls -la
                '''
            }
        }
    }
}

pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                    // Runs the container as user ID 1000 to prevent permission locks
                    args '-u 1000:1000'
                }
            }
            steps {
                sh '''
                ls -la
                node --version
                npm  --version
                npm ci
                npm run build
                ls -la
                '''
            }
        }
        stage('Test'){
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                    args '-u 1000:1000'
                }
            }
            steps {
                sh '''
                echo 'Testing stage'
                test -f build/index.html
                echo $?
                npm test
                '''
            }
        }
        stage('E2E'){
            agent {
                docker {
                    // Playwright also needs to run with matching host user permissions
                    image 'mcr.microsoft.com/playwright:v1.61.0-noble'
                    reuseNode true
                    args '-u 1000:1000 --ipc=host'
                }
            }
            steps {
                sh '''
                npm install serve
                node_modules/.bin/serve -s build &
                sleep 15
                npx playwright test
                '''
            }
        }
    }
    post {
        always {
            junit 'test-results/junit.xml'
        }
    }
}
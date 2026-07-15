pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                    args '-u 1000:1000 --ipc=host --env npm_config_cache=/tmp/.npm'
                }
            }
            steps {
                sh '''
                ls -la
                node --version
                npm --version
                # ONLY delete node_modules, keep package-lock.json intact
                rm -rf node_modules 
                npm ci
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
                    args '-u 1000:1000 --ipc=host --env npm_config_cache=/tmp/.npm'
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
        stage('E2E') {
            agent {
                docker {
                    // Playwright also needs the npm cache fix to run 'npm install' successfully
                    image 'mcr.microsoft.com/playwright:v1.61.0-noble'
                    reuseNode true
                    args '-u 1000:1000 --ipc=host --env npm_config_cache=/tmp/.npm'
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
            // Prevents the post-action stage from failing the build if tests are skipped
            junit allowEmptyResults: true, testResults: 'test-results/junit.xml'
        }
    }
}
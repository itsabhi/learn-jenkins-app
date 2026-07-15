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

        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
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

        // stage('E2E') {
        //     agent {
        //         docker {
        //             image 'mcr.microsoft.com/playwright:v1.61.0-noble'
        //             reuseNode true
        //             args '-u 1000:1000 --ipc=host'
        //         }
        //     }
        //     steps {
        //         sh '''
        //         set -e
        //         npm install serve
        //         node_modules/.bin/serve -s build &
        //         sleep 15
        //         npx playwright test
        //         '''
        //     }
        // }
    }

    // post {
    //     always {
    //         junit testResults: 'test-results/junit.xml', allowEmptyResults: true
    //     }
    // }
}
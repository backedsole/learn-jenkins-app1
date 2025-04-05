pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '6abdadaf-58c4-4992-bfa9-f1a566e8ce00'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }


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
                    #test -f build/index.html
                    npm test
                '''
            }
            post {
                always {
                    junit 'test-results/junit.xml'
                }
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
                    ./node_modules/.bin/serve -s build & sleep 10
                    npx playwright test --reporter=html
                '''
            }
            post {
                always {
                    publishHTML (target : [allowMissing: false,
                      alwaysLinkToLastBuild: true,
                      keepAll: true,
                      reportDir: 'playwright-report',
                      reportFiles: 'index.html',
                      reportName: 'All Reports',
                      reportTitles: 'Test Report'])
                }
            }
        }     
        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                npx netlify-cli --version
                npm install netlify-cli
                ./node_modules/.bin/netlify --version
                ./node_modules/.bin/netlify status
                echo "Deploying to $NETLIFY_SITE_ID token: $NETLIFY_AUTH_TOKEN"
                ./node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }
        }
    }
}

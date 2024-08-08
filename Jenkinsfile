pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = 'de23ecd5-21ad-46e7-930b-91805f6cee43'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }
    stages {
        // stage('Build') {
        //     agent {
        //         docker {
        //             image 'node:18-alpine'
        //             reuseNode true
        //         }
        //     }
        //     steps {
        //         sh '''
        //             ls -la
        //             node --version
        //             npm --version
        //             npm ci
        //             npm run build
        //             ls -la
        //         '''
        //     }
        // }
        stage('Run Tests') {
            parallel {
                stage('Unit tests') {
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    } 
                    steps {
                        sh '''
                        echo "Test stage"
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
                        npx playwright test
                        '''
                    }
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
                   echo 'hhhh'
                   npm install netlify-cli
                   node_modules/.bin/netlify --version
                   echo "Deploying to production. SITE ID $NETLIFY_SITE_ID"
                   node_modules/.bin/netlify status
                   node_modules/.bin/netlify deploy --dir=build --prod
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
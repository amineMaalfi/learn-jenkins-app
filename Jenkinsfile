pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = 'de23ecd5-21ad-46e7-930b-91805f6cee43'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
        CI_ENVIRONMENT_URL = 'https://chipper-halva-7a38fd.netlify.app'
    }
    stages {
        // stage('Run Tests') {
        //     parallel {
        //         stage('Unit tests') {
        //             agent {
        //                 docker {
        //                     image 'node:18-alpine'
        //                     reuseNode true
        //                 }
        //             } 
        //             steps {
        //                 sh '''
        //                 echo "Test stage"
        //                 test -f build/index.html
        //                 npm test
        //                 '''
        //             }
        //         }

        //         stage('E2E') {
        //             agent {
        //                 docker {
        //                     image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
        //                     reuseNode true
        //                 }
        //             } 
        //             steps {
        //                 sh '''
        //                 npm install serve
        //                 node_modules/.bin/serve -s build & 
        //                 sleep 10
        //                 npx playwright test
        //                 '''
        //             }
        //         }
        //     }
        // }

        stage('Deploy Staging') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                   npm install netlify-cli
                   node_modules/.bin/netlify --version
                   echo "Deploying to production. SITE ID $NETLIFY_SITE_ID"
                   node_modules/.bin/netlify status
                   node_modules/.bin/netlify deploy --dir=build
                '''
            }
        }

        stage('Deploy prod') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                   npm install netlify-cli
                   node_modules/.bin/netlify --version
                   echo "Deploying to production. SITE ID $NETLIFY_SITE_ID"
                   node_modules/.bin/netlify status
                   node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }
        }

        stage('Prod E2E') {
                agent {
                    docker {
                        image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                        reuseNode true
                    }
                }
                environment {
                    CI_ENVIRONMENT_URL = 'https://chipper-halva-7a38fd.netlify.app'
                } 
                steps {
                    sh '''
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
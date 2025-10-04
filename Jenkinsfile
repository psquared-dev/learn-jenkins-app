pipeline {
    agent {
        docker {
            image "mcr.microsoft.com/playwright:v1.39.0-jammy"       
            args '-u root:root'                          
        }
    }

    environment {
        NETLIFY_SITE_ID = "72ef1930-26cf-4a65-95cb-13d383e323f9"
        NETLIFY_AUTH_TOKEN = credentials('NETLIFY_SECRET')
    }

    stages {
        stage('Build') {
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

        stage("Test"){
            steps {
                sh '''
                    test -f build/index.html
                    npm test
                '''
            }
        }

        stage("E2E Tests"){            
            steps {
                sh '''
                    npm install -g serve
                    serve -s build &
                    sleep 10
                    npx playwright test --reporter=html
                '''
            }

        }

        stage("Staging Deploy"){
            steps {
                echo "Deploying to server..."
                sh '''
                    npm install -g netlify-cli@20.1.1
                    netlify --version
                    echo "Deploying to Netlify... $NETLIFY_SITE_ID"  
                    netlify status
                    netlify deploy --dir=build 
                    echo Done!
                '''
            }
        }

        stage("Prod E2E"){
            environment {
                CI_ENVIRONMENT_URL = "https://tranquil-salamander-69a084.netlify.app"
            }

            steps {
                sh '''
                    npx playwright test --reporter=html
                '''
            }
        }

    }

    post{
        always {
            junit 'jest-results/junit.xml'
            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
        }
    }
}

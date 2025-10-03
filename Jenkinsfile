pipeline {
    agent {
        docker {
            image "mcr.microsoft.com/playwright:v1.55.0-noble"                                 
        }
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
                    npx playwright test
                '''
            }

        }
    }

    post{
        always {
            junit 'test-results/junit.xml'
        }
    }
}

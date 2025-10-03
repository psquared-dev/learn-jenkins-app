pipeline {
    agent {
        docker {
            image "node:18-alpine"
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
            agent {
                docker {
                    image "mcr.microsoft.com/playwright:v1.55.0-noble"             
                    args '-v /var/run/docker.sock:/var/run/docker.sock'       
                }
            }

            steps {
                sh '''
                    npm install -g serve
                    serve -s build
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

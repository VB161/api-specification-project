pipeline {
    agent any

    environment {
        OPENAPI_VALIDATOR = '/usr/local/bin/swagger-cli'
        DEPLOYMENT_SERVER = 'http://api-server.company.com'
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/your-username/api-specification-project.git'
            }
        }
        stage('Validate OpenAPI') {
            steps {
                script {
                    sh '''
            $OPENAPI_VALIDATOR validate openapi.yaml
            '''
                }
            }
        }
        stage('Deploy to Staging') {
            steps {
                script {
            sh '''
            curl -X PUT -H "Content-Type: application/yaml" \
            --data-binary @openapi.yaml \
            $DEPLOYMENT_SERVER/specifications/sales-api
            '''
                }
            }
        }
        stage('Run API Tests') {
            steps {
                script {
            sh '''
            python3 run_api_tests.py --spec openapi.yaml --url $DEPLOYMENT_SERVER
            '''
                }
            }
        }
    }

    post {
        success {
            slackSend channel: '#api-deployments',
                       message: "✅ API Specification deployed successfully!"
        }
        failure {
            slackSend channel: '#api-deployments',
                       message: "❌ API Deployment FAILED: Check logs in Jenkins"
        }
    }
}

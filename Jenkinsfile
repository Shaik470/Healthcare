pipeline {
    agent any

    options {
        timestamps()
        timeout(time: 10, unit: 'MINUTES')
        disableConcurrentBuilds()
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Setup Python') {
            steps {
                sh '''
                    python3 -m venv .venv
                    .venv/bin/python -m pip install \
                      --disable-pip-version-check -r requirements.txt
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                    .venv/bin/python -m pytest -v
                '''
            }
        }

        stage('Smoke Test') {
            steps {
                sh '''
                    .venv/bin/python -c "
from app import app
response = app.test_client().get('/health')
print('HTTP status:', response.status_code)
print('Response:', response.get_json())
assert response.status_code == 200
assert response.get_json()['status'] == 'healthy'
"
                '''
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: Checkout, tests and health check completed.'
        }
        failure {
            echo 'FAILURE: Check the first failing stage in Console Output.'
        }
        always {
            echo 'Pipeline execution finished.'
        }
    }
}

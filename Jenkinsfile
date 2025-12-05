pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/dedfish101/simple_python_web_app.git'
            }
        }

        stage('Install') {
            steps {
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                    . venv/bin/activate
                    pytest --maxfail=1 --disable-warnings -q
                '''
            }
        }

        stage('Security Scan (Trivy)') {
            steps {
                sh '''
                    echo "=== Running Trivy Scan (SARIF) ==="
                    trivy fs . \
                      --format sarif \
                      --output trivy-report.sarif \
                      --exit-code 0
                '''
            }
        }
    }

    post {
        always {
            recordIssues(
                id: 'aquasec-trivy',
                name: 'Aquasec Trivy Warnings',
                tools: [sarif(pattern: 'trivy-report.sarif')]
            )

            archiveArtifacts artifacts: 'trivy-report.sarif', allowEmptyArchive: true
        }
    }
}

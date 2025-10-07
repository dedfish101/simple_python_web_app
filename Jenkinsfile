pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/sarthak20052005/simple_python_web_app.git'
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

        stage('Security Scan') {
            steps {
                sh '''
                    # Scan requirements.txt for vulnerabilities
                    trivy fs --format json --output trivy-report.json requirements.txt
                '''
            }
        }
    }

    post {
        always {
            // This block contains the final, correct syntax for the Warnings plugin
            recordIssues(
                tools: [trivy(pattern: 'trivy-report.json')],
                failOnError: true,
                qualityGates: [
                    // FINAL FIX: The parameter is 'type' and the values are uppercase strings
                    [threshold: 1, type: 'HIGH', unstable: false],
                    [threshold: 1, type: 'ERROR', unstable: false] // 'ERROR' is used for CRITICAL severity
                ]
            )
        }
    }
}
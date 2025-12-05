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

        stage('Security Scan - Trivy FS') {
            steps {
                sh '''
                    echo "Running Trivy vulnerability scan on project files..."

                    # This scans the local folder (fs = filesystem)
                    # --exit-code 0 → build NEVER fails even if vulnerabilities exist
                    trivy fs \
                        --format json \
                        --output trivy-report.json \
                        --exit-code 0 \
                        .
                '''
            }
        }

        /*
        stage('Security Scan - Trivy Image') {
            steps {
                sh '''
                    echo "Building Docker image for scanning..."
                    docker build -t simple_app:latest .

                    echo "Running Trivy scan on Docker image..."
                    trivy image \
                        --format json \
                        --output trivy-image-report.json \
                        --exit-code 0 \
                        simple_app:latest
                '''
            }
        }
        */
    }

    post {
        always {
            echo "Archiving Trivy reports..."
            archiveArtifacts artifacts: 'trivy-report.json', allowEmptyArchive: true
            // archiveArtifacts artifacts: 'trivy-image-report.json', allowEmptyArchive: true
        }
    }
}

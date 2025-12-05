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
        archiveArtifacts artifacts: 'trivy-report.json', allowEmptyArchive: true
    }
}

}

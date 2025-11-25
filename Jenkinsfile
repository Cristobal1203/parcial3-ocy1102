pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t taskmanager-secure .'
            }
        }

        stage('Run App Container') {
            steps {
                bat 'docker run -d --rm -p 5000:5000 --name taskmanager-secure taskmanager-secure'
            }
        }

        stage('Security Scan - ZAP') {
            steps {
                bat 'docker run --rm -t owasp/zap2docker-stable zap-baseline.py -t http://host.docker.internal:5000 -r zap_report.html'
            }
        }

        stage('Stop App Container') {
            steps {
                bat 'docker stop taskmanager-secure || exit /b 0'
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'zap_report.html', followSymlinks: false
        }
    }
}

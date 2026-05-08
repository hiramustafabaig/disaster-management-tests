pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t disaster-tests .'
            }
        }

        stage('Run Tests in Docker') {
            steps {
                sh 'docker run --rm disaster-tests'
            }
        }
    }

    post {
        always {
            script {

                // safer fallback approach for email
                def email = "dev.devneuron@gmail.com"

                try {
                    email = sh(
                        script: "git log -1 --pretty=format:%ae",
                        returnStdout: true
                    ).trim()
                } catch (Exception e) {
                    echo "Could not extract git email, using fallback"
                }

                echo "Sending email to: ${email}"

                emailext (
                    to: email,
                    subject: "Jenkins CI Results - Disaster Tests #${env.BUILD_NUMBER}",
                    body: """
Hello,

Pipeline has completed.

Project: Disaster Management Tests
Build Number: ${env.BUILD_NUMBER}
Build Status: ${currentBuild.currentResult}

View details: ${env.BUILD_URL}

Regards,
Jenkins CI Pipeline
""",
                    attachLog: true,
                    mimeType: 'text/plain'
                )
            }

            echo "Pipeline finished"
        }
    }
}
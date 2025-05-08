pipeline {
    agent any

    triggers {
        cron('H 3 * * *') // Daily at 3 AM
    }

    environment {
        BUILD_DIR = 'build'
        TEST_RESULTS_DIR = 'test-results'
        RECIPIENTS = 'your-email@example.com'
    }

    stages {
        stage('Checkout & Build') {
            steps {
                echo 'Checking out code...'
                checkout scm
                echo 'Building the project...'
                sh 'echo "Building the project..."'

            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                sh 'echo "Running tests..."' // or 'npm test', 'pytest', 'mvn test', etc.
            }
            post {
                always {
                    junit "${TEST_RESULTS_DIR}/**/*.xml"
                }
            }
        }

        stage('Archive') {
            steps {
                echo 'Archiving artifacts...'
                archiveArtifacts artifacts: "${BUILD_DIR}/**/*, ${TEST_RESULTS_DIR}/**/*", fingerprint: true
            }
        }

        stage('Report') {
            steps {
                script {
                    def status = currentBuild.result ?: 'SUCCESS'
                    emailext (
                        to: "${RECIPIENTS}",
                        subject: "Build #${env.BUILD_NUMBER} - ${status}",
                        body: """<p>Build Status: ${status}</p>
                                 <p>Check the build: ${env.BUILD_URL}</p>""",
                        mimeType: 'text/html'
                    )
                }
            }
        }
    }
}

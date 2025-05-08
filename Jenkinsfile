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
                // Example build command: npm install, mvn install, etc.
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
                // Replace this with the actual test command that generates XML result files
                sh 'echo "Running tests..."' // or 'npm test', 'pytest', 'mvn test', etc.
            }
            post {
                always {
                    script {
                        def testResults = "${TEST_RESULTS_DIR}/**/*.xml"
                        if (fileExists(testResults)) {
                            junit testResults
                        } else {
                            echo 'No test result files found. Skipping JUnit report.'
                        }
                    }
                }
            }
        }

        stage('Archive') {
            steps {
                echo 'Archiving artifacts...'
                // Ensure that the build and test-results directories are correctly populated
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

// Jenkinsfile (Place this file in the root directory of your repository)

pipeline {
    // Define the execution environment for the whole pipeline. 
    // We use a Python Docker image since that's where Pytest runs.
    agent {
        docker {
            image 'python:3.10-alpine'
            // Ensure the container can access the host machine's network
            // to reach the running WordPress instance (e.g., http://localhost:8000)
            args '--network host' 
        }
    }

    environment {
        // Define the base URL for the WordPress REST API that your tests will hit
        WP_API_URL = 'http://localhost:8000/wp-json/wp/v2' 
    }

    stages {

        // ===================================
        // Stage 1 & 2: Build & Setup (Python)
        // ===================================
        stage('Setup Pytest Environment') {
            steps {
                echo "Installing Python dependencies from the 'project' folder..."
                // Use a requirements file if you have one, or install directly
                sh 'pip install pytest requests' 
                echo 'Pytest dependencies installed successfully.'
            }
        }
        
        // ===================================
        // Stage 3: Test Stage (Your Pytest Part)
        // ===================================
        stage('Run API Tests (Pytest)') {
            steps {
                echo "Running API tests in the 'project/tests' directory..."
                // Execute Pytest, telling it to look in your 'project' folder.
                // --html: Generates the HTML report (required deliverable).
                sh "pytest project/tests/ --html=project/tests/test_report.html --self-contained-html"
            }
            post {
                always {
                    // Archive the generated test report so you can access it in Jenkins
                    archiveArtifacts artifacts: 'project/tests/test_report.html', fingerprint: true
                }
                failure {
                    echo 'Tests FAILED. Stopping pipeline before deployment.'
                    // Fail the stage if tests fail
                    currentBuild.result = 'FAILURE'
                }
            }
        }

        // ===================================
        // Stage 4: Staging Deployment
        // ===================================
        stage('Deploy to Staging Environment') {
            steps {
                echo 'Tests passed. Starting Staging Deployment...'
                
                // *** IMPORTANT: Insert your actual deployment command here. ***
                // This is where you would call a deployment script or a tool like Argo CD/AWS CodeDeploy.
                // Example using a simple shell script:
                // sh './deploy_script.sh staging'

                // Placeholder command:
                sh 'echo "Deployment successful to staging server." && sleep 5' 
            }
        }
    }
    
    post {
        success {
            echo 'Pipeline completed successfully. Staging deployment done. 🎉'
        }
    }
}

pipeline {
    agent any

    environment {
        PROJECT_ID = 'thinking-seer-455809-p6'
        GOOGLE_APPLICATION_CREDENTIALS = credentials('gcp-service-account')  // Service account credential
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/AneilRapole/appengine-2025.git'
            }
        }

        // stage('Install Dependencies') {
        //     steps {
        //         script {
        //             sh '''
        //             # Update apt package list
        //             sudo apt-get update -y
                    
        //             # Install Python3 and development tools if not installed
        //             sudo apt-get install -y python3-pip python3-dev python3-venv bash
                    
        //             # Create a virtual environment
        //             python3 -m venv venv
                    
        //             # Activate the virtual environment
        //             . venv/bin/activate  # Using dot (.) instead of 'source'
                    
        //             # Upgrade pip inside the virtual environment
        //             pip install --upgrade pip
                    
        //             # Install dependencies from requirements.txt
        //             pip install -r requirements.txt
        //             '''
        //         }
        //     }
        // }

        stage('Deploy to Google App Engine') {
            steps {
                script {
                    // Check gcloud installation and working directory
                    sh 'gcloud --version'
                    sh 'pwd'
                    sh 'ls -l'  // List files to verify app.yaml presence

                    // Authenticate with Google Cloud
                    sh 'gcloud auth activate-service-account --key-file=${GOOGLE_APPLICATION_CREDENTIALS}'

                    // Set the GCP project
                    sh 'gcloud config set project $PROJECT_ID'

                    // Deploy the application to App Engine
                    sh 'gcloud app deploy --bucket=gs://thinking-seer-455809-p6-deployments --quiet'
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            cleanWs()  // Cleans workspace
        }

        success {
            echo 'Deployment successful!'
        }

        failure {
            echo 'Deployment failed!'
        }
    }
}

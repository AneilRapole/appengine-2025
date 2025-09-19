pipeline {
    agent any

    environment {
        PROJECT_ID = 'thinking-seer-455809-p6'
        GOOGLE_APPLICATION_CREDENTIALS = credentials('gcp-service-account')  // Your Jenkins GCP credential
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/AneilRapole/appengine-2025.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    sh '''
                    # Install Python venv if not installed
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install --upgrade pip
                    pip install -r requirements.txt
                    '''
                }
            }
        }

        stage('Deploy to Google App Engine') {
            steps {
                script {
                    // Authenticate and set project
                    sh "gcloud auth activate-service-account --key-file=${GOOGLE_APPLICATION_CREDENTIALS}"
                    sh "gcloud config set project ${PROJECT_ID}"

                    // Ensure App Engine app exists
                    sh '''
                    if ! gcloud app describe; then
                        gcloud app create --region=us-central
                    fi
                    '''

                    // Remove .gcloudignore if it exists (prevents files being skipped)
                    sh 'rm -f .gcloudignore'

                    // Deploy using app.yaml with timestamped version
                    sh '''
                    gcloud app deploy app.yaml \
                        --version=jenkins-$(date +%Y%m%d%H%M%S) \
                        --quiet
                    '''
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning workspace...'
            cleanWs()
        }
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}

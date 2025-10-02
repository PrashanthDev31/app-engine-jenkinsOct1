pipeline {
    agent any

    environment {
        PROJECT_ID = 'testapp-472401'
        GOOGLE_APPLICATION_CREDENTIALS = credentials('gcp-service-account')  // Service account credential
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/PrashanthDev31/app-engine-jenkinsOct1'
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    sh '''
                        # Update apt package list
                        sudo apt-get update -y
                    
                        # Install Python3 and development tools if not installed
                        sudo apt-get install -y python3-pip python3-dev python3-venv bash
                    
                        # Create a virtual environment
                        python3 -m venv venv
                    
                        # Activate the virtual environment
                        . venv/bin/activate
                    
                        # Upgrade pip inside the virtual environment
                        pip install --upgrade pip
                    
                        # Install dependencies from requirements.txt
                        pip install -r requirements.txt
                    '''
                }
            }
        }

        stage('Deploy to Google App Engine') {
            steps {
                script {
                    sh 'gcloud --version'
                    sh 'pwd'
                    sh 'ls -l'

                    // Authenticate with Google Cloud
                    sh 'gcloud auth activate-service-account --key-file=${GOOGLE_APPLICATION_CREDENTIALS}'

                    // Set the GCP project
                    sh 'gcloud config set project $PROJECT_ID'

                    // Deploy the application to App Engine
                    sh 'gcloud app deploy --quiet'
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            cleanWs()   // no node wrapper needed
        }

        success {
            echo 'Deployment successful!'
        }

        failure {
            echo 'Deployment failed!'
        }
    }
}

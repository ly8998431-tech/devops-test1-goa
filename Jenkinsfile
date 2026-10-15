pipeline {
    agent any
    triggers {
        pollSCM('H/1 * * * *')
    }
    stages {
        stage('Checkout') {
            steps { checkout scm }
        }
        stage('Install Dependencies') {
            steps { echo 'HTML static website: no dependencies required' }
        }
        stage('Build') {
            steps {
                bat 'if not exist index.html exit /b 1'
                bat 'findstr /C:"DevOps CI/CD Test" index.html'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploy stage - configure hosting credentials to publish website'
            }
        }
    }
}

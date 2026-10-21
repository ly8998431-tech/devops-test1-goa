pipeline {
    agent any

    triggers {
        pollSCM('H/1 * * * *')
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checkout source code from GitHub'
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Static HTML project - no dependencies required'
                bat 'node --version'
                bat 'npm --version'
            }
        }

        stage('Build') {
            steps {
                echo 'Checking website files...'
                bat 'if not exist index.html exit /b 1'
                bat 'findstr /I "<title>" index.html'
                echo 'Build Success'
            }
        }

        stage('Deploy') {
            steps {
                withCredentials([
                    string(
                        credentialsId: 'netlify-token',
                        variable: 'NETLIFY_AUTH_TOKEN'
                    ),
                    string(
                        credentialsId: 'netlify-site-id',
                        variable: 'NETLIFY_SITE_ID'
                    )
                ]) {
                    bat 'set "npm_config_cache=%WORKSPACE%\\.npm-cache" && npx --yes netlify-cli@latest deploy --prod --dir . --site "%NETLIFY_SITE_ID%" --auth "%NETLIFY_AUTH_TOKEN%"'
                }
            }
        }
    }

    post {
        success {
            echo 'PIPELINE SUCCESS'
            echo 'Website: https://effortless-griffin-565515.netlify.app'
            
            powershell '''
                $token = "8993138145:AAF0uEtnaiU74ZuM4gDKlCW0uL-s7xhmGTQ"
                $chatId = "880945958"
                $msg = "✅ PIPELINE SUCCESS!`nWebsite: https://effortless-griffin-565515.netlify.app`nJob: $env:JOB_NAME - Build #$env:BUILD_NUMBER"
                Invoke-RestMethod -Uri "https://api.telegram.org/bot$token/sendMessage" -Method Post -Body @{ chat_id = $chatId; text =$msg }
            '''
        }

        failure {
            echo 'PIPELINE FAILED - Check Console Output'
            
            powershell '''
                $token = "8993138145:AAF0uEtnaiU74ZuM4gDKlCW0uL-s7xhmGTQ"
                $chatId = "880945958"
                $msg = "❌ PIPELINE FAILED!`nJob: $env:JOB_NAME - Build #$env:BUILD_NUMBER`nCheck Console Output!"
                Invoke-RestMethod -Uri "https://api.telegram.org/bot$token/sendMessage" -Method Post -Body @{ chat_id = $chatId; text =$msg }
            '''
        }
    }
}
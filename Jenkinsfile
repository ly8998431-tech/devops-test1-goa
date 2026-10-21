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

            script {
                try {
                    withCredentials([
                        string(
                            credentialsId: 'telegram-token',
                            variable: 'TELEGRAM_BOT_TOKEN'
                        )
                    ]) {
                        powershell '''
                            $ErrorActionPreference = 'Stop'
                            $chatId = '8809459598'
                            $msg = "✅ DEPLOY SUCCESS`nProject: devops-test`nBranch: main`nURL: https://effortless-griffin-565515.netlify.app"

                            $uri = "https://api.telegram.org/bot$env:TELEGRAM_BOT_TOKEN/sendMessage"

                            Invoke-RestMethod `
                                -Uri $uri `
                                -Method Post `
                                -Body @{
                                    chat_id = $chatId
                                    text = $msg
                                } | Out-Null

                            Write-Host 'Telegram success notification sent'
                        '''
                    }
                } catch {
                    echo 'Telegram success notification failed. Check credentials and chat ID.'
                }
            }
        }

        failure {
            echo 'PIPELINE FAILED - Check Console Output'

            script {
                try {
                    withCredentials([
                        string(
                            credentialsId: 'telegram-token',
                            variable: 'TELEGRAM_BOT_TOKEN'
                        )
                    ]) {
                        powershell '''
                            $ErrorActionPreference = 'Stop'
                            $chatId = '8809459598'
                            $msg = "❌ DEPLOY FAILED`nProject: devops-test`nBranch: main`nJob: $env:JOB_NAME - Build #$env:BUILD_NUMBER`nCheck Jenkins Console Output."

                            $uri = "https://api.telegram.org/bot$env:TELEGRAM_BOT_TOKEN/sendMessage"

                            Invoke-RestMethod `
                                -Uri $uri `
                                -Method Post `
                                -Body @{
                                    chat_id = $chatId
                                    text = $msg
                                } | Out-Null

                            Write-Host 'Telegram failure notification sent'
                        '''
                    }
                } catch {
                    echo 'Telegram failure notification failed. Check credentials and chat ID.'
                }
            }
        }
    }
}

pipeline {
    agent any

    tools {
        nodejs 'Node JS 23'
    }

    environment {
        REPO_URL = 'https://github.com/np0307/Tenzies_Game.git'
        BUILD_DIR = 'build'
        PORT = '5000'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git url: "${env.REPO_URL}", branch: 'main'
            }
        }

        stage('Install Dependencies') {
            steps {
                echo 'Installing node modules...'
                bat 'npm install'
            }
        }

        stage('Build App') {
            steps {
                echo 'Building the React app...'
                bat 'npm run build'
            }
        }

        stage('Archive Build') {
            steps {
                echo 'Archiving build artifacts...'
                archiveArtifacts artifacts: 'build/**', fingerprint: true
            }
        }
    }

    post {
        success {
            echo '✅ Build completed successfully.'
            echo '🧹 Stopping any existing server on port 5000...'
            catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                bat '''
                for /F "tokens=5" %%a in ('netstat -aon ^| findstr :5000') do taskkill /F /PID %%a
                '''
            }

            echo '🚀 Starting local HTTP server...'
            bat '''
            start "" cmd /c "http-server build -p 5000"
            '''
        }
        failure {
            echo '❌ Build failed. Check console output.'
        }
    }
}

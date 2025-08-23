pipeline {
    agent any

    tools {
        nodejs 'Node'   // must match your Jenkins NodeJS tool config
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('Build') {
            steps {
                bat 'npm run build'
            }
        }

        stage('Archive Build') {
            steps {
                archiveArtifacts artifacts: 'build/**', fingerprint: true
            }
        }

        stage('Deploy (serve)') {
            steps {
                bat '''
                echo Stopping old serve processes if any...
                REM Ignore error if no node.exe is running
                taskkill /F /IM node.exe >nul 2>&1 || ver >nul

                echo Starting React app with serve on port 5000...
                start /B cmd /c "npx serve -s build -l 5000 > serve-app.log 2>&1"
                echo React app is running. Logs: serve-app.log
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline finished successfully! React app is running at http://localhost:5000"
        }
        failure {
            echo "❌ Pipeline failed! Check logs for errors."
        }
        always {
            echo "Build completed at: ${env.BUILD_URL}"
        }
    }
}

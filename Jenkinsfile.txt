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
                taskkill /F /IM node.exe || echo "No old serve process found"

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

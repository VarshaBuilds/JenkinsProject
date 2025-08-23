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

        stage('Deploy (PM2)') {
            steps {
                bat '''
                echo Deploying React app with PM2...

                REM Install PM2 globally if not already installed
                call npm install -g pm2

                REM Stop old process if it exists
                pm2 delete react-app || echo "No old app found"

                REM Start new process using PM2
                pm2 start npx --name "react-app" -- serve -s build -l 5000

                echo React app is running with PM2 on port 5000
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

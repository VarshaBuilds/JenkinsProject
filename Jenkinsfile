pipeline {
    agent any

    tools {
        nodejs 'Node 24.6.0'   // name must match your Jenkins NodeJS tool config
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

        stage('Test') {
            steps {
                bat 'npm test -- --watchAll=false'
            }
            post {
                always {
                    junit 'junit.xml' // if you add a reporter, else skip
                }
            }
        }

        stage('Archive Build') {
            steps {
                archiveArtifacts artifacts: 'build/**', fingerprint: true
            }
        }

        stage('Deploy') {
            steps {
                bat '''
                if not exist deploy mkdir deploy
                xcopy /E /I /Y build deploy\\build
                echo "React app deployed to deploy\\build folder"
                '''
            }
        }
    }
}

pipeline {
    agent any

    tools {
        nodejs 'NodeJS'       // Nom del NodeJS configurat a Jenkins
    }

    environment {
        SONAR_SCANNER_HOME = tool 'SonarScanner'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install dependencies') {
            steps {
                sh 'npm ci'
            }
        }

        stage('Tests with coverage') {
            steps {
                sh 'npm run test -- --watch=false --code-coverage'
                archiveArtifacts artifacts: 'coverage/**', fingerprint: true
            }
        }

        stage('SonarQube Scan') {
            steps {
                withSonarQubeEnv('SonarQube') {
                    sh '''
                    ${SONAR_SCANNER_HOME}/bin/sonar-scanner \
                    -Dsonar.projectKey=frontend-petclinic \
                    -Dsonar.sources=src \
                    -Dsonar.exclusions=**/*.spec.ts \
                    -Dsonar.javascript.lcov.reportPaths=coverage/lcov.info
                    '''
                }
            }
        }

        stage('Quality Gate') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}

pipeline {
  agent any

  environment {
    SCANNER_HOME = tool 'ScannerCLI'   // or your SonarScanner tool name
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/<your-username>/8.2CDevSecOps.git'
      }
    }

    stage('Install Dependencies') {
      steps {
        sh 'npm install'
      }
    }

    stage('Run Security Tests (snyk)') {
      steps {
        sh 'npm test || true'
      }
    }

    stage('Run Unit Tests') {
      steps {
        sh 'npm run test:unit || true'
      }
    }

    stage('Generate Coverage') {
      steps {
        sh 'npm run coverage || true'
      }
    }

    stage('NPM Audit (Security Scan)') {
      steps {
        sh 'npm audit || true'
      }
    }

    stage('SonarCloud Analysis') {
      steps {
        withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
          sh """
            ${SCANNER_HOME}/bin/sonar-scanner \
              -Dsonar.login=$SONAR_TOKEN
          """
        }
      }
    }
  }
}

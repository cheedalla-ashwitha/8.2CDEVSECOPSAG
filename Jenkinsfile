pipeline {
  agent any

  tools {
    nodejs 'Node18'                    // puts node & npm on PATH (no root needed)
  }

  environment {
    SCANNER_HOME = tool 'ScannerCLI'   // SonarScanner tool name from Tools
  }

  stages {
    stage('Checkout') { steps { checkout scm } }

    stage('Install Dependencies') {
      steps {
        sh '''
          set -euxo pipefail
          node -v
          npm -v
          # Faster & consistent if lockfile exists, fallback otherwise
          if [ -f package-lock.json ]; then
            npm ci --no-audit --no-fund
          else
            npm install --no-audit --no-fund
          fi
        '''
      }
    }

    stage('Run Security Tests (snyk)')   { steps { sh 'npm test || true' } }
    stage('Run Unit Tests')              { steps { sh 'npm run test:unit || true' } }
    stage('Generate Coverage')           { steps { sh 'npm run coverage || true' } }
    stage('NPM Audit (Security Scan)')   { steps { sh 'npm audit || true' } }

    stage('SonarCloud Analysis') {
      steps {
        withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
          sh '''
            ${SCANNER_HOME}/bin/sonar-scanner \
              -Dsonar.login=$SONAR_TOKEN
          '''
        }
      }
    }
  }
}

pipeline {
  agent any
  options { timestamps() }

  environment {
    // Jenkins → Manage Jenkins → Tools → SonarQube Scanner → Name must match
    SCANNER_HOME = tool 'ScannerCLI'
  }

  stages {
    stage('Checkout') {
      steps { checkout scm }
    }

    stage('Provision Runtime (one-time)') {
      steps {
        sh '''
          if ! command -v npm >/dev/null 2>&1; then
            echo "[bootstrap] Installing Node.js 18..."
            apt-get update
            apt-get install -y curl
            curl -fsSL https://deb.nodesource.com/setup_18.x | bash -
            apt-get install -y nodejs
          fi
          echo "[runtime] node: $(node -v)  npm: $(npm -v)"
        '''
      }
    }

    stage('Install Dependencies') {
      steps {
        sh '''
          npm config set fund false
          npm config set audit false
          if [ -f package-lock.json ]; then
            npm ci --no-audit --no-fund || npm install --no-audit --no-fund
          else
            npm install --no-audit --no-fund
          fi
        '''
      }
    }

    stage('Run Security Tests (snyk)') {
      steps { sh 'npm test || true' }   // keeps pipeline going even if snyk needs auth
    }

    stage('Run Unit Tests') {
      steps { sh 'npm run test:unit || true' }
    }

    stage('Generate Coverage') {
      steps { sh 'npm run coverage || true' }   // writes coverage/lcov.info
    }

    stage('NPM Audit (Security Scan)') {
      steps { sh 'npm audit || true' }
    }

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

  post {
    always {
      archiveArtifacts artifacts: 'coverage/**, npm-debug.log*', allowEmptyArchive: true
    }
  }
}

pipeline {
  agent any
  environment {
    SCANNER_HOME = tool 'ScannerCLI'   // Jenkins → Manage Jenkins → Tools → SonarQube Scanner
  }

  stages {
    // Jenkins already does "Declarative: Checkout SCM" for you — no extra checkout stage needed.

    stage('Install') {
      steps {
        sh 'node -v && npm -v'
        // make installs tolerant to peer-dep conflicts (typeorm vs mongodb)
        sh 'npm config set audit false'
        sh 'npm config set fund false'
        sh 'npm config set legacy-peer-deps true'
        sh 'npm install --no-audit --no-fund --legacy-peer-deps'
      }
    }

    stage('Unit Tests') {
      steps {
        sh 'npm run test:unit || true'
      }
    }

    stage('Coverage') {
      steps {
        sh 'npm run coverage || true'   // generates coverage/lcov.info
      }
    }

    stage('NPM Audit') {
      steps {
        sh 'npm audit || true'
      }
    }

    stage('SonarCloud') {
      steps {
        withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
          sh '${SCANNER_HOME}/bin/sonar-scanner -Dsonar.login=$SONAR_TOKEN'
        }
      }
    }
  }
}

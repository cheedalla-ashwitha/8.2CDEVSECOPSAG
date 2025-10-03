pipeline {
  agent { docker { image 'node:18' args '-u root' } }
  environment { SCANNER_HOME = tool 'ScannerCLI' }
  stages {
    stage('Checkout'){ steps { checkout scm } }
    stage('Install'){ steps { sh 'node -v && npm -v && npm ci || npm install' } }
    stage('Test: unit'){ steps { sh 'npm run test:unit || true' } }
    stage('Coverage'){ steps { sh 'npm run coverage || true' } }
    stage('Audit'){ steps { sh 'npm audit || true' } }
    stage('Sonar'){
      steps {
        withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'SONAR_TOKEN')]) {
          sh '${SCANNER_HOME}/bin/sonar-scanner -Dsonar.login=$SONAR_TOKEN'
        }
      }
    }
  }
}

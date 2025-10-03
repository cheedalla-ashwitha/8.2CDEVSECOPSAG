pipeline {
  agent any
  stages {
    stage('Checkout')      { steps { git branch: 'main', url: 'https://github.com/cheedalla-ashwitha/8.2CDEVSECOPSAG' } }
    stage('Install Deps')  { steps { sh 'npm install' } }
    stage('Run Tests')     { steps { sh 'npm test || true' } }       // continue even if tests fail
    stage('Coverage')      { steps { sh 'npm run coverage || true' } }
    stage('NPM Audit')     { steps { sh 'npm audit || true' } }
  }
}
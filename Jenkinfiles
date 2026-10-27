pipeline {
  agent any

  // Needs the NodeJS plugin and a NodeJS installation named 'node-22'
  // (Manage Jenkins > Tools > NodeJS installations). The app requires Node >=20.19.
  tools {
    nodejs 'node-22'
  }

  options {
    timeout(time: 20, unit: 'MINUTES')
    disableConcurrentBuilds()
    buildDiscarder(logRotator(numToKeepStr: '10'))
  }

  environment {
    CI = 'true'
    NEXT_TELEMETRY_DISABLED = '1'
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Install') {
      steps {
        sh 'node --version'
        sh 'npm --version'
        // npm ci installs exactly what is in package-lock.json
        sh 'npm ci'
      }
    }

    stage('Lint') {
      steps {
        sh 'npm run lint'
      }
    }

    stage('Test') {
      steps {
        // Runs only if a "test" script exists in package.json
        sh 'npm run test --if-present'
      }
    }

    stage('Build') {
      steps {
        // Also runs the TypeScript check
        sh 'npm run build'
      }
    }
  }

  post {
    success {
      echo 'Lint, tests and build all passed.'
    }
    failure {
      echo 'The pipeline failed. Check the stage logs above.'
    }
    cleanup {
      cleanWs()
    }
  }
}
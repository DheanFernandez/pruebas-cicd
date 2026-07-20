pipeline {
  agent any

  environment {
    BASE_URL = credentials('BASE_URL')
    PLAYWRIGHT_JUNIT_OUTPUT_NAME = 'test-results/results.xml'
  }

  options {
    timeout(time: 60, unit: 'MINUTES')
    timestamps()
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Install deps') {
      steps {
        bat 'npm ci'
      }
    }

    stage('Install browsers') {
      steps {
        bat 'npx playwright install'
      }
    }

    stage('Run tests') {
      steps {
        bat 'if not exist test-results mkdir test-results'
        bat 'npx playwright test --reporter=html,line,junit'
      }
    }
  }

  post {
    always {
      archiveArtifacts(
        artifacts: 'playwright-report/**',
        fingerprint: true,
        allowEmptyArchive: true
      )

      publishHTML(target: [
        reportDir            : 'playwright-report',
        reportFiles          : 'index.html',
        reportName           : 'Playwright Report',
        keepAll              : true,
        allowMissing         : true,
        alwaysLinkToLastBuild: true
      ])

      junit(
        allowEmptyResults: true,
        testResults: 'test-results/*.xml'
      )
    }
  }
}
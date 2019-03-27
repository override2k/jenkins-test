pipeline {
  agent any

  environment {
    TEST='test1'
    // BUILD_TAG = sh(returnStdout: true, script: 'git tag -l --points-at HEAD').trim()
    // BUILD_BRANCH = sh(returnStdout: true, script: 'git rev-parse --abbrev-ref HEAD').trim()
  }

  options {
    parallelsAlwaysFailFast()
  }

  stages {
    stage('Build') {
      steps {
        sh 'echo do build'
        echo sh(returnStdout: true, script: 'env')
      }
    }
    stage('Test') {
      steps {
        parallel(
          'Test branch 1': {
            sh 'echo OK'
          },
          'Test branch 2': {
            sh 'echo OK'
          },
        )
      }
    }
    stage('Deploy dev') {
      when { branch 'master' }
      steps {
        sh 'echo build master - dev '
      }
    }
    stage('Deploy staging') {
      when { tag pattern: "^\\d+\\.\\d+\\.\\d+\\.sta\$", comparator: "REGEXP"}
      steps {
        sh 'echo build master -staging'
      }
    }
    stage('Deploy master') {
      when { tag pattern: "^\\d+\\.\\d+\\.\\d+\$", comparator: "REGEXP"}
      steps {
        sh 'echo build master - production'
      }
    }
  }
  post {
      always {
          sh 'echo Done!'
      }
  }
}

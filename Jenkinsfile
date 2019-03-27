pipeline {
  agent any

  environment {
    TEST='test1'
  }

  options {
    parallelsAlwaysFailFast()
  }

  stages {
    stage('Build') {
      steps {
        sh 'echo do build'
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
      when {  allOf { branch 'master'; tag "*.sta" } }
      steps {
        sh 'echo build master -staging'
      }
    }
    // stage('Deploy master') {
    //   when { allOf { branch 'master'; buildingTag() } }
    //   steps {
    //     sh 'echo build master - production'
    //   }
    // }
  }
  post {
      always {
          sh 'echo Done!'
      }
  }
}

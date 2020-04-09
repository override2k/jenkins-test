pipeline {
  // agent {
  //   docker { image 'php:7.4-cli' }
  // }
  agent none

  environment {
    TEST='test1'
    // BUILD_TAG = sh(returnStdout: true, script: 'git tag -l --points-at HEAD').trim()
    // BUILD_BRANCH = sh(returnStdout: true, script: 'git rev-parse --abbrev-ref HEAD').trim()
  }

  options {
    parallelsAlwaysFailFast()
  }

  stages {
    stage('Sidecar') {
      steps {
        script {
          docker.image('mysql:5').withRun('-e "MYSQL_ROOT_PASSWORD=my-secret-pw" -p 3306:3306') { c ->
              /* Wait until mysql service is up */
              sh 'while ! mysqladmin ping -h0.0.0.0 --silent; do sleep 1; done'
              /* Run some tests which require MySQL */
              sh mysql -V
          }
        }
      }
    }

    stage('Build') {
      agent { 
        docker {
          label 'docker'
          image 'php:7.4-cli'
        }
      }
      steps {
        sh 'echo do build'
        echo sh(returnStdout: true, script: 'env')
        sh 'php -v'
        githubNotify description: 'Build step ok',  status: 'SUCCESS'
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

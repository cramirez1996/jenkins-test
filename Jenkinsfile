pipeline {

  environment {
    registry = "nginx"
    dockerImage = ""
  }

  agent any

  stages {

    stage('Checkout Source') {
      steps {
        git 'https://github.com/cramirez1996/jenkins-test'
      }
    }

  }

}

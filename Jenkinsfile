pipeline {
  
  agent { label 'kubepod' }

  stages {
    
    stage('Init') {
      def dockerHome = tool 'myDocker'
      env.PATH = "${dockerHome}/bin:${env.PATH}"
    }

    stage('Checkout Source') {
      steps {
        git 'https://github.com/cramirez1996/jenkins-test'
      }
    }
    
    stage('Build Image') {
      steps {
        script {
          myapp = docker.build("cramirez96/jenkinstest:${env.BUILD_ID}")
        }
      }
    }
    
    stage('Push Image'){
      steps {
        script {
          docker.withRegistry('https://registry.hub.docker.com', 'dockerhub'){
            myapp.push('latest')
            myapp.push('${env.BUILD_ID}')
          }
        }
      }
    }
    
    stage('Deploy App') {
      steps {
        script {
          kubernetesDeploy(configs: "k8s.yml", kubeconfigId: "kubeconfig")
        }
      }
    }

  }

}

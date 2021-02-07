pipeline {
  
  agent { label 'kubepod' }

  stages {
  
    stage('Checkout Source') {
      steps {
        git 'https://github.com/cramirez1996/jenkins-test'
      }
    }
    
    stage('Build Image') {
      steps {
        script {
          myapp = docker.build("localhost:30100/jenkinstest:${env.BUILD_ID}")
        }
      }
    }
    
    stage('Push Image'){
      steps {
        script {
          docker.withRegistry('http://localhost:30100', 'cramirez96DockerHub'){
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

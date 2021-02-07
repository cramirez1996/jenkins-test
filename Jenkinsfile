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
          myapp = docker.build("cramirez96/jenkinstest:${env.BUILD_ID}")
        }
      }
    }
    
    stage('Push Image'){
      steps {
        script {
          docker.withRegistry('https://registry.hub.docker.com', 'cramirez96DockerHub'){
            myapp.push('latest')
            myapp.push("${env.BUILD_ID}")
          }
        }
      }
    }
    
    stage('Deploy App') {
      steps {
        script {
          def inptext = readFile file: "k8s.yml" 
          inptext = inptext.replaceAll("{BUILD_ID}", "${env.BUILD_ID}")       
          writeFile file: "k8s.yml", text: inptext
          kubernetesDeploy(configs: "k8s.yml", kubeconfigId: "kubeconfig")
        }
      }
    }

  }

}

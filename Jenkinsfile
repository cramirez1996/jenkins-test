def getNamespace(branchName){
  if(branchName == 'origin/master'){
    return "prod";
  } else if (branchName == 'origin/development') {
    return "dev";
  } else if (branchName == 'origin/testing') {
    return "testing";
  } else {
    return "";
  }
}

pipeline {
  
  agent none
  
  
  environment {
    NAMESPACE = getNamespace(GIT_BRANCH)
    REGISTRY = "247092817324.dkr.ecr.us-east-2.amazonaws.com/test"
    ECR_CREDENTIALS = "eks-deployer"
    ECR_REGION = "ecr:eu-central-1:"
  }

  stages {

     stage('Build Image') {
       agent {
      docker { image 'node:14-alpine' }
    }
      steps {
        script {
          repo = docker.build("${REGISTRY}:${env.BUILD_ID}")
        }
      }
    }

    stage('Push Image'){
      agent {
        docker { image 'node:14-alpine' }
      }
      steps {
        script {
          docker.withRegistry('https://' + REGISTRY, ECR_REGION + ECR_CREDENTIALS){
            myapp.push("${NAMESPACE}-latest")
            myapp.push("${NAMESPACE}-${env.BUILD_ID}")
          }
        }
      }
    }
            
    stage('Modify YML') {
      agent { 
        kubernetes{
            label 'jenkins-slave'
        }        
    }
      steps {
        script {
          def inptext = readFile file: "k8s.yml"
          inptext = inptext.replaceAll("\\{BUILD_ID\\}", "${NAMESPACE}-${env.BUILD_ID}")       
          inptext = inptext.replaceAll("NAMESPACE", "${NAMESPACE}")       
          writeFile file: "k8s.yml", text: inptext
          
        }
      }
    }

    // stage('Change Directory') {
    //   steps {
    //       sh script: """
    //       #!/bin/bash
    //       cd $WORKSPACE/jenkins-test/
    //       ls
    //       """
    //   }
    // }

    stage('Install kubectl') {
      agent { 
        kubernetes{
            label 'jenkins-slave'
        }        
    }
      steps {
          sh script: '''
          #get kubectl for this demo
          curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
          chmod +x ./kubectl
          '''
      }
    }

    stage('Deploy') {
      agent { 
        kubernetes{
            label 'jenkins-slave'
        }        
    }
      steps {
          sh script: """
          ./kubectl apply -f k8s.yml
          """
      }
    }
  }

}
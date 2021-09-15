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
  
  
  agent { 
        kubernetes{
            label 'jenkins-slave'
        }
        
    }
  
  environment {
    NAMESPACE = getNamespace(GIT_BRANCH)
  }

  stages {

    stage('Testing Functionality') {
      steps {
          sh script: """
          ls
          echo $NAMESPACE
          """
      }
    }
  
    // stage('Checkout Source') {
    //   steps {
    //     git 'https://github.com/cramirez1996/jenkins-test'
    //   }
    // }

    // stage('Clone Repository') {
    //   steps{
    //       sh(script: '''
    //           git clone https://github.com/cramirez1996/jenkins-test.git
    //       ''', returnStdout: true) 
    //   }
    // }
            
    stage('Modify YML') {
      steps {
        script {
          def inptext = readFile file: "k8s.yml"
          inptext = inptext.replaceAll("\\{BUILD_ID\\}", "latest")       
          inptext = inptext.replaceAll("\\{NAMESPACE\\}", "${NAMESPACE}")       
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
      steps {
          sh script: '''
          #get kubectl for this demo
          curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
          chmod +x ./kubectl
          '''
      }
    }

    stage('Deploy') {
      steps {
          sh script: """
          ./kubectl apply -f k8s.yml
          """
      }
    }
  }

}
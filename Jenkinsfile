pipeline {
  environment {
    registry = "https://279773871986.dkr.ecr.us-east-2.amazonaws.com"
    repository = "sc-test-vuln"
    registryCredential = 'ecr:us-east-2:ecr'
    dockerImage = ""
  }

  agent any
  stages {
    stage("Cloning Git Repo") {
      steps {
        git "https://github.com/robmaynardjr/SC-Test-Vuln.git"
      }
    }

    stage("Building image") {
      steps{
        script {
          dockerImage = docker.build('279773871986.dkr.ecr.us-east-2.amazonaws.com/sc-test-vuln:latest')
        }
      }
    }

    stage("Stage Image") {
      steps{
        script {
          docker.withRegistry((registry + "/" + repository), registryCredential ) {
            dockerImage.push()
          }
        }
      }
    }

    stage("Smart Check Scan") {
        steps {
            withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding', 
                    credentialsId: 'ecr', 
                    accessKeyVariable: 'AWS_ACCESS_KEY_ID', 
                    secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'                
            ]]){            
                smartcheckScan([
                    imageName: "279773871986.dkr.ecr.us-east-2.amazonaws.com/sc-test-vuln",
                    smartcheckHost: "10.0.10.100",
                    insecureSkipTLSVerify: true,
                    smartcheckCredentialsId: "smart-check-jenkins-user",
                    imagePullAuth: new groovy.json.JsonBuilder([
                        aws: [
                            region: "us-east-2"                    
                        ]   
                        ]).toString(),
                    ])
                }
            }
        }
        

    stage ("Deploy to Cluster") {
      steps{
        echo "Function to be added at a later date."
      }
    }
  }
}
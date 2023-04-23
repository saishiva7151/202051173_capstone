pipeline {
    agent any

     environment{
       registryCredential = 'ecr:ap-northeast-1:saishiva'
       appRegistry = "298451811635.dkr.ecr.ap-northeast-1.amazonaws.com/202051173_capstone"
       capstoneRegistry = "https://298451811635.dkr.ecr.ap-northeast-1.amazonaws.com"
       cluster = "202051173_capstone"
        service = "srinivas"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/saishiva7151/202051173_capstone.git'
            }
        }

       stage("Docker Build Image"){
           steps{
             script{
                dockerImage = docker.build(appRegistry+ ":$BUILD_NUMBER",".")
             }
           }
      }

       stage('Test Website') {
            steps {
                // Run tests on the website
                sh 'echo "Running tests on the website"'
            }
       }

       stage("Upload App Image"){
         steps{
            script{
                docker.withRegistry(capstoneRegistry, registryCredential){
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                }
            }
         }
      }

      stage('Deploy to ecs'){
         when {
                branch "master"
         }
         steps{
            withAWS(credentials: 'saishiva', region: 'ap-northeast-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}

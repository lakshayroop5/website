pipeline {
    agent any

     environment{
       registryCredential = 'ecr:ap-northeast-1:202051110'
       appRegistry = "648966252005.dkr.ecr.ap-northeast-1.amazonaws.com/202051110"
       capstoneRegistry = "https://648966252005.dkr.ecr.ap-northeast-1.amazonaws.com"
       cluster = "202051110"
        service = "202051110project"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/lakshayroop5/website'
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
            withAWS(credentials: '202051110', region: 'ap-northeast-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}

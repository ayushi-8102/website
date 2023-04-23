pipeline {
    agent any

     environment{
       registryCredential = 'ecr:ap-northeast-1:Ayushi8102'
       appRegistry = "309018573251.dkr.ecr.ap-northeast-1.amazonaws.com/ayushi-capstone-project"
       capstoneRegistry = "https://309018573251.dkr.ecr.ap-northeast-1.amazonaws.com"
       cluster = "CapstoneProject"
        service = "CapstoneProject"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/ayushi-8102/website'
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
            withAWS(credentials: 'Ayushi8102', region: 'ap-northeast-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}

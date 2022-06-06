// pipeline{
//     agent any
//     stages{
//         stage('Git clone'){
//             steps{
//                 git 'https://github.com/minhvuong0499/python-app.git'
//             }
//         }  
//         stage('Create Docker image'){
//             steps{
//                 sh 'docker build -t python-app .'
//                 sh 'docker run  -p 5000:5000 -d python-app'
//             }
//         }
//     }
// }
pipeline{
    agent any
    stages{
        stage('Git Clone'){
            steps{
                git branch: ,//<name branch> ==> master
                credentialsId: ,//<your credentials id>
                url: //<your github url>
            }
        }
    }
//----------------------------------------------------------------------------------------------------------------------------------------------------------    
    stages('Docker push'){
        steps{
            script{
                withAWS(region: 'ap-southeast-1', credentials: 'awsId') {
                    sh "${ecrLogin()}"
                    sh "docker tag <NAME_IMAGES> <URI_ECR>"
                    sh "docker push <URI_ECR>"
            }
        }
    }
}
//----------------------------------------------------------------------------------------------------------------------------------------------------------
    try { //If you are not using CodeDeploy for your ECS services 
        withAWS(region: 'ap-southeast-1', credentials: 'awsId') {
            def updateService = "aws ecs update-service --service <your-ecs-service-name> --cluster <your-cluster-name> --force-new-deployment"
            def runUpdateService = sh(returnStdout: true, script: updateService)
            def serviceStable = "aws ecs wait services-stable --service $internationalService --cluster $internationalCluster"
            sh(returnStdout: true, script: serviceStable)
    // put all your slack messaging here
  }
} catch(Exception e) {
  echo e.message.toString()
}
//----------------------------------------------------------------------------------------------------------------------------------------------------------    
    try {// If you are using CodeDeploy for blue green deployment
        withAWS(region: 'ap-southeast-1', credentials: 'awsId') {
            sh(returnStdout: true, script: "aws s3 cp appspec.yml s3://<your-s3-bucket-name>/appspec.yml")
            def deploy = sh(returnStdout: true, script: "aws deploy create-deployment \
            --application-name <application-name> \
            --deployment-group-name <deployment-group-name> \
            --s3-location bucket=<your-s3-bucket-name>,bundleType=yaml,key=appspec.yml")
            def deployJson = readJSON text: deploy, returnPojo: true
            def waitDeploy = "aws deploy wait deployment-successful --deployment-id ${deployJson['deploymentId']}"
            sh(returnStdout: true, script: waitDeploy)
    // your slack message here to alert you deployment is done
            sh(returnStdout: true, script: "aws s3 rm s3://<your-s3-bucket-name>/appspec.yml")
  }
 catch(Exception e) {
  echo e.toString()
  env.ERROR_MESSAGE = e.toString()
  currentBuild.result = 'FAILURE'
}
//----------------------------------------------------------------------------------------------------------------------------------------------------------
}//END
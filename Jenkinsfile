node
{
    def mavenHome= tool name: "maven-3.6.2"
     echo "the job nme is: ${env.JOB_NAME}"
    properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])
    
    try{  
 stage('CheckoutCode'){
git branch: 'development', credentialsId: '7aac4c71-c5c4-448c-b6cb-8b12fb121ed5', url: 'https://github.com/j-vidya20/maven-web-application.git' 
}

 stage('Build'){ 
 sh "${mavenHome}/bin/mvn clean package"
}
stage('ExecuteSonarQubeReport'){
 sh "${mavenHome}/bin/mvn sonar:sonar"
}
  /* 
stage('UploadAtrifactsIntoNexus'){
sh "${mavenHome}/bin/mvn deploy"
}
stage('DeployApplicationIntoTomcatServer'){
  sshagent(['5040e2cf-f477-4319-89d2-b915156da4db']) {
    sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@13.233.168.164:/opt/apache-tomcat-9.0.65/webapps/"
}
}
*/
    }
catch(e){
    currentBuild.result = "FAILED"
    throw e   
}
    finally {
    // Success or failure, always send notifications
    notifyBuild(currentBuild.result)
  }
}//node ends

def slacknotification(String buildStatus = 'STARTED') {
  // build status of null means successful
  buildStatus =  buildStatus ?: 'SUCCESSFUL'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESSFUL') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary)
}


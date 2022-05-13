import groovy.json.JsonSlurper

def getFtpPublishProfile(def publishProfilesJson) {
  def pubProfiles = new JsonSlurper().parseText(publishProfilesJson)
  for (p in pubProfiles)
    if (p['publishMethod'] == 'FTP')
      return [url: p.publishUrl, username: p.userName, password: p.userPWD]
}

pipeline {
    withEnv(['AZURE_SUBSCRIPTION_ID=c36066bf-9ecf-4d40-a364-61048b396ec3',
        'AZURE_TENANT_ID=<tenant_id>'])
    agent none
    stages { 
        stage ("Clone source") {
             agent {
                docker {
                    image 'maven:3.8.1-adoptopenjdk-11'
                    args '-v /tmp/.m2:/root/.m2'
                }
            }
            steps{
                git branch: 'master',
                            url: 'https://github.com/mzegarras/labmaven.git'
                 sh 'mvn package'
            }
        }
        stage ("Publish package") {
            agent any
             steps{
                 archiveArtifacts(artifacts: "target/*.jar",onlyIfSuccessful:true)
             }
        }
        
        stage('deploy') {
            agent any
            def resourceGroup = 'TallerJavaDevOps-13052022'
            def webAppName = 'taller-java-devops'
            // login Azure
            withCredentials([usernamePassword(credentialsId: 'AzureServicePrincipal', passwordVariable: 'AZURE_CLIENT_SECRET', usernameVariable: 'AZURE_CLIENT_ID')]) {
            sh 'az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID'
            sh 'az account set -s $AZURE_SUBSCRIPTION_ID'
      }
            // get publish settings
            def pubProfilesJson = sh script: "az webapp deployment list-publishing-profiles -g $resourceGroup -n $webAppName", returnStdout: true
            def ftpProfile = getFtpPublishProfile pubProfilesJson
            // upload package
            sh "curl -T target/*.war $ftpProfile.url/webapps/ROOT.war -u '$ftpProfile.username:$ftpProfile.password'"
            // log out
            sh 'az logout'
    }
    }
}

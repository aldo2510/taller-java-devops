import groovy.json.JsonSlurper

pipeline {
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

      
      
      
    }
}

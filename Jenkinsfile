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

                 stash(
                     name:"build-test-artifacts",
                     includes: "**/target/surefire-reports/TEST-*.xml,target/*.jar,"
                 )
            }
        }


        stage ("Publish package") {
            agent any
             steps{
                // unstash "build-test-artifacts"

                // junit "**/target/surefire-reports/TEST-*.xml"

                 archiveArtifacts(artifacts: "target/*.jar",onlyIfSuccessful:true)

                // jacoco( 
                //    execPattern: 'target/*.exec',
                //    classPattern: 'target/classes',
                //    sourcePattern: 'src/main/java',
                //    exclusionPattern: 'src/test*'
                //)

             }
        }
    }
}

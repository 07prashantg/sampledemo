@Library('groovy-library') _

pipeline {
    agent any 

    parameters{
        choice(name: 'action', choices: 'create\ndelete', description: 'Choose create/Destroy')
        // string(name: 'ImageName', description: "name of the docker build", defaultValue: 'javapp')
        // string(name: 'ImageTag', description: "tag of the docker build", defaultValue: 'v1')
        // string(name: 'DockerHubUser', description: "name of the Application", defaultValue: 'prashant1107')
    }

    stages {
        stage('Git Checkout'){
                    when { expression {  params.action == 'create' } }
            steps{
            gitCheckout(
                branch: "main",
                url: "https://github.com/07prashantg/sampledemo.git"
            )
            }
        }
        
        stage('Maven Build : maven'){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   mvnBuild()
                   'pwd'
               }
            }
        }
        
        stage("Saving in local") {
            steps {
                script {
                    // Assuming your JAR file is in the target directory
                    def jarFileName = sh(script: 'ls target/*.jar', returnStdout: true).trim()
                    echo("i got file")

                    cp "${jarFileName}" "/home/ec2-user"
                }
            }
        }
    }
}

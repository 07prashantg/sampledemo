@Library('groovy-library') _

pipeline {
    agent any 

    parameters{
        choice(name: 'action', choices: 'create\ndelete', description: 'Choose create/Destroy')
    }

    environment {
        REMOTE_SERVER_IP = '15.206.151.75'
        REMOTE_SERVER_USER = 'ec2-user'
        REMOTE_SERVER_PATH = '/home/ec2-user'
        //CREDENTIALS_ID = 'server-access'
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
                    echo("Found JAR file: ${jarFileName}")

                    
                    // sh "chmod 600 pemfile"

                    // Copying JAR file to remote server
                    // withCredentials([file(credentialsId: 'pemfile', variable: 'KEYFILE')]) {
                    // def privateKeyContent = readFile(KEYFILE).trim()
                    // sh "echo '${privateKeyContent}' > private_key.pem" // Writing content to a file
                    // sh "chmod 600 private_key.pem" // Setting correct permissions

                    // Use the private key file for scp
                    //sh "sudo chmod 400 /home/ec2-user/key.pem"

                    sh "whoami"
                    sh "ls -l /var/lib/jenkins/key.pem"
                    sh "cat /var/lib/jenkins/key.pem"

                    sh "chmod 400 /var/lib/jenkins/key.pem"
                    sh "chown jenkins:jenkins /var/lib/jenkins/key.pem"

                    sh "scp -i /var/lib/jenkins/key.pem -o StrictHostKeyChecking=no target/*.jar ${REMOTE_SERVER_USER}@${REMOTE_SERVER_IP}:${REMOTE_SERVER_PATH}"
                    echo("Copied JAR file to remote server")
                    
                    
                }
            }
        }
    }
}

@Library('groovy-library') _

pipeline {
    agent any 

    parameters{
        choice(name: 'action', choices: 'create\ndelete', description: 'Choose create/Destroy')
    }

    environment {
        REMOTE_SERVER_IP = '65.2.187.26'
        REMOTE_SERVER_USER = 'ec2-user'
        REMOTE_SERVER_PATH = '/home/ec2-user'
        CREDENTIALS_ID = 'server-access'
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

                    //sh "cp target/*.jar /home/ec2-user/"
                    // withCredentials([sshUserPrivateKey(credentialsId: 'server-access', keyFileVariable: 'SSH_PRIVATE_KEY')]){

                    def privateKeyContent = credentials(CREDENTIALS_ID)
                    def privateKeyFile = writeFile file: 'private_key.pem', text: privateKeyContent
                    sh "chmod 600 private_key.pem"
                    sh "scp -i private_key.pem ${jarFileName} ${REMOTE_SERVER_USER}@${REMOTE_SERVER_IP}:${REMOTE_SERVER_PATH}/"
                    echo("Copied JAR file to remote server")
                }
            }
        }
    }
}

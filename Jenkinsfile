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
                    echo("Found JAR file: ${jarFileName}")

                    // Retrieve private key from Jenkins credential
                    def privateKeyContent = ''

                    // Inject credential into environment
                    withCredentials([sshUserPrivateKey(credentialsId: CREDENTIALS_ID, keyFileVariable: 'SSH_PRIVATE_KEY')]) {
                        privateKeyContent = env.SSH_PRIVATE_KEY
                    }

                    // Create a temporary private key file
                    def privateKeyFile = writeFile file: 'private_key.pem', text: privateKeyContent
                    sh "cat private_key.pem"
                    sh "chmod 600 private_key.pem"

                    // Copying JAR file to remote server
                    sh "scp -i private_key.pem ${jarFileName} ${REMOTE_SERVER_USER}@${REMOTE_SERVER_IP}:${REMOTE_SERVER_PATH}/"
                    echo("Copied JAR file to remote server")
                }
            }
        }
    }
}

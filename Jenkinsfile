@Library('groovy-library') _

pipeline {
    agent any 

    parameters{
        choice(name: 'action', choices: 'create\ndelete', description: 'Choose create/Destroy')
    }

    environment {
        REMOTE_SERVER_IP = '3.110.117.85'
        REMOTE_SERVER_USER = 'ec2-user'
        REMOTE_SERVER_PATH = '/home/ec2-user'
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
                    withCredentials([sshUserPrivateKey(credentialsId: 'jar-deploy', keyFileVariable: 'SSH_PRIVATE_KEY')]){
                        sh "scp -i $SSH_PRIVATE_KEY target/*.jar ${REMOTE_SERVER_USER}@${REMOTE_SERVER_IP}:${REMOTE_SERVER_PATH}/"
                    }
                }
            }
        }
    }
}

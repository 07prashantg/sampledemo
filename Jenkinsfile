pipeline {
    agent any 
    stages {
        stage("Git checkout") {
            steps {
                script {
                    git 'https://github.com/07prashantg/sampledemo.git'
                }
            }
        }
        
        stage("Maven Build") {
            steps {
                script {
                    sh "mvn clean install -DskipTests"
                }
            }
        }
        
        stage("Saving in local") {
            steps {
                script {
                    // Assuming your JAR file is in the target directory
                    def jarFileName = sh(script: 'ls target/*.jar', returnStdout: true).trim()
                    cp "${jarFileName}" "/Users/prashant/Desktop/Project1/"
                }
            }
        }
    }
}

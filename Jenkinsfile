pipeline {
    agent any
    
    environment {
        registry = "003866745935.dkr.ecr.ap-southeast-1.amazonaws.com/nasir"
    }

    stages {
        stage('Checkout') {
            steps {
               checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/nasir19noor/spring-boot']])
            }
        }
        stage("Build Jar") {
            steps {
                sh "mvn clean install"
            }
        }
        stage ("Build Image") {
            steps {
                script {
                    dockerImage = docker.build registry
                    dockerImage.tag("$BUILD_NUMBER")
                }
            }
            
        }
        stage ("Push Image") {
            steps {
                script {
                    sh "aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin 003866745935.dkr.ecr.ap-southeast-1.amazonaws.com"
                    sh "docker push 003866745935.dkr.ecr.ap-southeast-1.amazonaws.com/nasir:$BUILD_NUMBER"
                }
            }
            
        }
        stage ("Helm Deploy"){
           steps {
               script {
                   sh "helm upgrade first --install mychart --namespace helm-deployment --set image.tag=$BUILD_NUMBER"
               }
           } 
        }
    }
}

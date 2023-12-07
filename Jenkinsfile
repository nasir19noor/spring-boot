pipeline {
   tools {
        maven 'Maven3'
    }
    agent any
    environment {
        registry = "003866745935.dkr.ecr.ap-southeast-1.amazonaws.com/nasir"
    }
   
    stages {
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/akannan1087/docker-spring-boot']]])     
            }
        }
      stage ('Build') {
          steps {
            sh 'mvn clean install'           
            }
      }
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry 
          dockerImage.tag("$BUILD_NUMBER")
        }
      }
    }
   
    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
     steps{  
         script {
                sh 'aws ecr get-login-password --region ap-southeast-1 | docker login --username AWS --password-stdin 003866745935.dkr.ecr.ap-southeast-1.amazonaws.com/nasir'
                sh 'docker push 003866745935.dkr.ecr.ap-southeast-1.amazonaws.com/nasir:$BUILD_NUMBER'
         }
        }
      }
        stage ('Helm Deploy') {
          steps {
            script {
                sh "helm upgrade first --install mychart --namespace helm-deployment --set image.tag=$BUILD_NUMBER"
                }
            }
        }
    }
}

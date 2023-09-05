pipeline {
   tools {
        maven 'Maven3'
    }
    agent any
    environment {
        registry = "517787923146.dkr.ecr.us-east-1.amazonaws.com/my-docker-app"
    }
   
    stages {
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/nahidkishore/Springboot_Microservices_App_EKS']]])     
            }
        }
      
        stage('Build') {
            steps {
                sh 'mvn clean install'           
            }
        }
      
        // Building Docker images
        stage('Building image') {
            steps {
                script {
                    dockerImage = docker.build registry 
                }
            }
        }
   
        // Uploading Docker images into AWS ECR
        stage('Pushing to ECR') {
            steps {  
                script {
                    sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 517787923146.dkr.ecr.us-east-1.amazonaws.com'
                    sh 'docker push 517787923146.dkr.ecr.us-east-1.amazonaws.com/my-docker-app:latest'
                }
            }
        }

        // K8S Deploy
        stage('K8S Deploy') {
            steps {
                script {
                    // Set the exec-api-version explicitly here
                    withKubeConfig([credentialsId: 'K8S', serverUrl: '']) {
                        sh ('kubectl apply -f eks-deploy-k8s.yaml')
                    }
                }
            }
        }
    }
}

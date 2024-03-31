pipeline {
    agent any
    
    tools {
        jdk 'jdk17'
        maven 'maven3'
        docker 'docker'
    }
    environment {
        IMAGE_REPO_NAME="project-repo"
        IMAGE_TAG="v1"
        AWS_ACCOUNT_ID="688329047339"
        AWS_DEFAULT_REGION="us-east-1"
        REPOSITORY_URI = "688329047339.dkr.ecr.us-east-1.amazonaws.com/project-repo"
    }
    
    stages {
        stage('git checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/surenthecourage/java-spring-petclinic.git'
            }
        }
        
        stage('Compile') {
            steps {
                sh "mvn compile"
            }
        }
        
        stage('Build') {
            steps {
                sh "mvn install -Dmaven.test.skip=true"
            }
        }
        
        // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
        }
      }
    }
    stage('Logging into AWS ECR') {
            steps {
                script {
                sh """aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com"""
                }  
            }
        }
   
    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
     steps{  
         script {
                sh """docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"""
                sh """docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"""
         }
        }
      }
    }
}

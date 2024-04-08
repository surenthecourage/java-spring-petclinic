pipeline {
    agent any
    
    tools {
        jdk 'jdk17'
        maven 'maven3'
    }
    environment {
        IMAGE_REPO_NAME="project-repo"
        IMAGE_TAG="v1"
        AWS_ACCOUNT_ID="954973595150"
        AWS_DEFAULT_REGION="us-east-1"
        REPOSITORY_URI = "954973595150.dkr.ecr.us-east-1.amazonaws.com/project-repo"
        ECR_CRED = ""ecr-cred
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

    stage('Pushing image') {
        steps{
            script {
                docker.withRegistry("$REPOSITORY_URI", "ecr:$AWS_DEFAULT_REGION:$ECR_CRED") {
                    docker.image("${IMAGE_REPO_NAME}:${IMAGE_TAG}").push()
                    }
                }
            }
        }
    }

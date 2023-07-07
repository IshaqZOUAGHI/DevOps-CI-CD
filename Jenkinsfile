pipeline {
  agent any
	
  environment {
    DOCKERHUB_USERNAME = 'ishaq'
    DOCKERHUB_PASSWORD = 'dckr_pat_i0VRS1etOICqWTmBVUovfBRC_Do'
    REMOTE_SERVER = '16.171.19.159'
    REMOTE_USER = 'ec2-user' 	
    DOCKER_IMAGE_NAME = 'devops'  	  
  }
	
  // Fetch code from GitHub
	
  stages {
    stage('checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/IshaqZOUAGHI/DevOps-CI-CD'

      }
    }
	  
   // Build Java application
	  
    stage('Maven Build') {
      steps {
        sh 'mvn clean install'
      }
	    
     // Post building archive Java application
	    
      post {
        success {
          archiveArtifacts artifacts: '**/target/*.jar'
        }
      }
    }
	  
  // Test Java application
	  
    stage('Maven Test') {
      steps {
        sh 'mvn test'
      }
    }
	  
   // Build docker image in Jenkins
	  
    stage('Build Docker Image') {

      steps {
        sh 'docker build -t devops:latest .'
        sh 'docker tag devops ishaq/devops:latest'
      }
    }
	  
   // Login to DockerHub before pushing docker Image
	  
    stage('Login to DockerHub') {
      steps {
                withCredentials([usernamePassword(credentialsId: '4c58011f-1423-4c2e-9c12-7efa16d05874', usernameVariable: 'ishaq', passwordVariable: 'dckr_pat_i0VRS1etOICqWTmBVUovfBRC_Do')]) {
                    sh """
                        docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASSWORD
                        docker build -t $DOCKER_IMAGE_NAME .
                    """
                }
            }
    }
	
	  
   // Push image to DockerHub registry
	  
    stage('Push Image to dockerHUb') {
      steps {
        sh 'docker push ishaq/devops:latest'
      }
      post {
        always {
          sh 'docker logout'
        }
      }

    }
	  
   // Pull docker image from DockerHub and run in EC2 instance 
	  
    stage('Deploy Docker image to AWS instance') {
      steps {
        script {
          sshagent(credentials: ['awscred']) {
          sh "ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${REMOTE_SERVER} 'docker stop javaApp || true && docker rm javaApp || true'"
	  sh "ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${REMOTE_SERVER} 'docker pull palakbhawsar/javawebapp'"
          sh "ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${REMOTE_SERVER} 'docker run --name javaApp -d -p 8081:8081 palakbhawsar/javawebapp'"
          }
        }
      }
    }
  }
}


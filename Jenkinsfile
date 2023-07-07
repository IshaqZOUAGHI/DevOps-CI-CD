pipeline {
  agent any
	
  environment {
    DOCKERHUB_USERNAME = 'ishaq'
    DOCKERHUB_PASSWORD = 'dckr_pat_7O3oWyfbLvS_dXs2PuGgIF8yknU'
    REMOTE_SERVER = '16.171.146.11'
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
                withCredentials([usernamePassword(credentialsId: 'docker-hub-cred', usernameVariable: 'ishaq', passwordVariable: 'dckr_pat_7O3oWyfbLvS_dXs2PuGgIF8yknU')]) {
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
      sh "ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${REMOTE_SERVER} 'docker pull ishaq/devops"
      	sh "ssh -o StrictHostKeyChecking=no ${REMOTE_USER}@${REMOTE_SERVER} 'docker run --name javaApp -d -p 8081:8081 ishaq/devops"
          }
        }
      }
    }
  }
}


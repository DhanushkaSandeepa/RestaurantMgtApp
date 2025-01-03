pipeline {
  agent {
    label "docker-agent"
  }
  environment {
    DOCKER_HUB_USER = 'your-dockerhub-username' // Replace with your Docker Hub username
    DOCKER_HUB_PASSWORD = credentials('dockerhub-credentials') // Jenkins credentials ID for Docker Hub
  }
  stages {
    stage ('Run Docker Compose') {
      steps {
        sh 'sudo docker-compose up -d'
      }
    }
    stage ('Build Docker Images') {
      steps {
        script {
          sh '''
          # Build the www service image
          docker build -t ${DOCKER_HUB_USER}/www:latest ./www

          # Tag other service images for consistency
          docker tag phpmyadmin/phpmyadmin:latest ${DOCKER_HUB_USER}/phpmyadmin:latest
          docker tag mysql:5.7.28 ${DOCKER_HUB_USER}/mysql:5.7.28
          '''
        }
      }
    }
    stage ('Push Docker Images to Docker Hub') {
      steps {
        script {
          withDockerRegistry(credentialsId: 'dockerhub-credentials') {
            sh '''
            # Push the www image
            docker push ${DOCKER_HUB_USER}/www:latest

            # Push other service images
            docker push ${DOCKER_HUB_USER}/phpmyadmin:latest
            docker push ${DOCKER_HUB_USER}/mysql:5.7.28
            '''
          }
        }
      }
    }
  }
}

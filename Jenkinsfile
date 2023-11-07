pipeline {
  agent any

  stages {
    stage('Build') {
      steps {
        script {
          // Build your Docker image
          sh 'docker build -t demoflaskapp .'
          // Tag your Docker image with the target registry/tag
          sh "docker tag demoflaskapp $DOCKER_BFLASK_IMAGE"
        }
      }
    }

    stage('Test') {
      steps {
        script {
          // Run your tests in the Docker container
          sh 'docker run demoflaskapp python -m pytest app/tests/'
        }
      }
    }

    stage('Deploy') {
      steps {
        withCredentials([usernamePassword(credentialsId: "${DOCKER_REGISTRY_CREDS}", passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
          script {
            // Log in to your Docker registry
            sh "echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USERNAME --password-stdin docker.io"
            // Push your Docker image to the registry
            sh "docker push $DOCKER_BFLASK_IMAGE"
          }
        }
      }
    }
  }

  post {
    always {
      // Log out from the Docker registry after the pipeline
      sh 'docker logout'
    }
  }
}

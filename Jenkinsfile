pipeline {
    
    agent any
    
    environment {
        imageName = "docker-image-name-here"
        registryCredentials = "jenkins saved nexus credentialsi"
        registry = "nexus-docker-hosted-dns-name:port-of-docker/"
        dockerImage = ''
    }
    
    stages {
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build imageName
        }
      }
    }
    // Uploading Docker images into Nexus Registry
    stage('Uploading to Nexus') {
     steps{  
         script {
             docker.withRegistry( 'http://'+registry, registryCredentials ) {
             dockerImage.push('latest')
          }
        }
      }
    }
    // Stopping Docker containers for cleaner Docker run
    stage('stop previous containers') {
         steps {
            sh 'docker ps -f name=docker-image-name -q | xargs --no-run-if-empty docker container stop'
            sh 'docker container ls -a -fname=docker-image-name -q | xargs -r docker container rm'
         }
       }    
    }
}

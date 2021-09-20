pipeline {
    
    agent any
    
    environment {
        imageName = "react-frontend-application"
        registryCredentials = "nexus"
        registry = "ec2-52-3-224-79.compute-1.amazonaws.com:8082/"
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
            sh 'docker ps -f name=react-frontend-application -q | xargs --no-run-if-empty docker container stop'
            sh 'docker container ls -a -fname=react-frontend-application -q | xargs -r docker container rm'
         }
       }
    stage('Docker Run') {
       steps{
         script {
                sh 'docker run -d -p 3000:3000 --rm --name react-frontend-application ' + registry + imageName
            }
         }
      }    
    }
}

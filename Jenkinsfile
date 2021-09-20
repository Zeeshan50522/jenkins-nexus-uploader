pipeline {
    
    agent any
    
    environment {
        imageName = "myphpapp"
        registryCredentials = "nexus"
        registry = "ec2-52-3-224-79.compute-1.amazonaws.com:8082/"
        dockerImage = ''
    }
    
    stages {
        stage('Code checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://bitbucket.org/ananthkannan/phprepo/']]])                   }
        }
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
            sh 'docker ps -f name=myphpcontainer -q | xargs --no-run-if-empty docker container stop'
            sh 'docker container ls -a -fname=myphpcontainer -q | xargs -r docker container rm'
         }
       }
    stage('Docker Run') {
       steps{
         script {
                sh 'docker run -d -p 3000:3000 --rm --name myphpcontainer ' + registry + imageName
            }
         }
      }    
    }
}

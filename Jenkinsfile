pipeline {
    
    agent any
    
    environment {
        imageName = "mywordpress"
        registryCredentials = "NEXUS_CRED"
        registry = "192.168.127.100:5000"
        dockerImage = ''
    }
    
    stages {
        stage('Code checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/Phuc03hp/wordpress_image.git']]])                   }
        }
    
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build -t imageName .
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
            sh 'docker ps -f name=mywordpress -q | xargs --no-run-if-empty docker container stop'
            sh 'docker container ls -a -fname=mywordpress -q | xargs -r docker container rm'
         }
       }
      
    stage('Docker Run') {
       steps{
         script {
                sh 'docker run -d -p 8888:80 --rm --name mywordpress ' + registry + imageName
            }
         }
      }    
    }
}

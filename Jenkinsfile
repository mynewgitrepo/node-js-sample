pipeline {
    agent any
        environment {
        //once you sign up for Docker hub, use that user_id here
        registry = "pavanktm/node-app"
        //- update your credentials ID after creating credentials for connecting to Docker Hub
        registryCredential = 'dockerhub'
        dockerImage = ''
    }
    stages {

        stage ('checkout') {
            steps {
            checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[url: 'https://github.com/mynewgitrepo/node-js-sample.git']]])
            }
        }
       
        stage ('Build docker image') {
            steps {
                script {
                dockerImage = docker.build registry + ":$BUILD_NUMBER"
                //dockerImage = docker.build registry + ":$BUILD_NUMBER"

                }
            }
        }
       
         // Uploading Docker images into Docker Hub
    stage('Upload Image') {
     steps{   
         script {
            docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
            }
        }
      }
    }
   
    stage ('K8S Deploy') {
        steps {
            script {
                sh 'kubectl apply -f deployment.yaml' 
                sh 'kubectl get all'
               
            }
        }
    }
  
    }  
}

always {
  // remove built docker image and prune system
  print 'Cleaning up the Docker system.'
  sh 'docker system prune -f'
}

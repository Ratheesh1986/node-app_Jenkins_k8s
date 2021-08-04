
      pipeline {
 agent any
  environment {
    registry = "logic2000/nodeapp"
    registryCredential = 'dockerhub_credentials'
    dockerImage = ''
  }
  stages {
    stage('get scm') {
      steps {
	  git credentialsId: 'github_credentials', url: 'https://github.com/Ratheesh1986/node-app_Jenkins_k8s.git'
       }
    }
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    stage('push image') {
      steps{
        script {
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
          }
        }
      }
    }
    stage('Remove old docker image') {
      steps{
        sh "docker rmi $registry:$BUILD_NUMBER"
      }
    }

   stage (k8s) {

       steps{

           sh "chmod +x changeTag.sh"
           sh "./changeTag.sh ${Docker_Tag}"
           sshagent(['kops-machine']) {
               sh "scp -o StrictHostKeyChecking=no services.yml node-app-pod.yml ec2-user@65.2.81.66:/home/ec2-user/"
           script{
               try{
                   sh "ssh ec2-user@65.2.81.66 kubectl apply -f ."
                   }catch(error){
                       sh"ssh ec2-user@65.2.81.66 kubectl create -f ."
 
                }  

            }
       
        }       
       

       }           
   }  
      
   }
       
   }

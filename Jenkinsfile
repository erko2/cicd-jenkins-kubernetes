pipeline {

  environment {
    azuredefaultapp = "kodeval/azuredefaultapp"
    azuregolangapp = "kodeval/azuregolangapp"
    azureotherapp = "kodeval/azureotherapp"
    dockerImage1 = ""
    dockerImage2 = ""
    dockerImage3 = ""
    
  }

  agent any

  stages {

    stage('Checkout Source') {
      steps {
        git 'https://github.com/erko2/cicd-jenkins-kubernetes.git'
      }
    }

    stage('Build images') {
      steps{
        script {
          dockerImage1 = docker.build ("azuredefaultapp", "-f ${env.WORKSPACE}/src/nginx/default/Dockerfile .")
          dockerImage2 = docker.build ("azuregolangapp" ,"-f ${env.WORKSPACE}/src/golang/Dockerfile .")
          dockerImage3 = docker.build ("azureotherapp","-f ${env.WORKSPACE}/src/nginx/other/Dockerfile .")
        }
      }
    }

    stage('Push to Dockerhub') {
      environment {
               registryCredential = 'dockerhublogin'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage1.push("latest")
            dockerImage2.push("latest")
            dockerImage3.push("latest")
          }
        }
      }
    }

    stage('Deploying to Kubernetes(azure)') {
      steps {
        script {
          kubernetesDeploy(configs: "deploymentservice.yml", kubeconfigId: "kubernetes")
        }
      }
    }

  }

}
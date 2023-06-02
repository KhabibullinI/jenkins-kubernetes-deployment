pipeline {

  environment {
    dockerimagename = "ilshat227/react-app"
    dockerImage = ""
  }

  agent {
      label 'my-jenkins-agent-nodejs'
  }

  stages {

    stage('Checkout Source') {
      steps {
        git branch: 'main',
            credentialsId: 'github-credential',
            url: 'https://github.com/KhabibullinI/jenkins-kubernetes-deployment'
      }
    }

    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build dockerimagename
        }
      }
    }

    stage('Pushing Image') {
      environment {
               registryCredential = 'dockerhub-credentials'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push("latest")
          }
        }
      }
    }

    stage('Deploying React.js container to.Kubernetes') {
      steps {
          sshagent(['k8s']) {
            sh 'scp -r -o StrictHostKeyChecking=no deployment.yaml service.yaml ilshat@172.27.106.213:/home/ilshat/'
          script {
            try{
                sh " ssh ilshat@172.27.106.213 kubectl apply -f ."
            }catch(error){
                sh "ssh ilshat@172.27.106.213 kubectl create -f ."
            }
          }
          
        }
      }
    }

  }

}
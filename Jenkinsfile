pipeline {
  environment {
    registry = "80081837/cicd-k8s-demo"
    registryCredential = 'dockerhubid'
    dockerImage = ''
  }
  agent any
  stages {
    stage('Compile') {
      steps {
        git 'https://github.com/yaswanth8008/springboot-app-aks-deployment.git'
        script{
                def mvnHome = tool name: 'MAVEN_HOME', type: 'maven'
                sh "${mvnHome}/bin/mvn package"
        }
      }
    }
    stage('Building Docker Image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    stage('Push Image To Docker Hub') {
      steps{
        script {
          /* Finally, we'll push the image with two tags:
                   * First, the incremental build number from Jenkins
                   * Second, the 'latest' tag.
                   * Pushing multiple tags is cheap, as all the layers are reused. */
          docker.withRegistry('https://registry.hub.docker.com', 'dockerhubid') {
              dockerImage.push("${env.BUILD_NUMBER}")
              dockerImage.push("latest")
          }
        }
      }
    }
    stage('Deploy to Kubernetes'){
        steps{
            sh 'kubectl apply -f deployment.yml'
       }
    }
  }
}

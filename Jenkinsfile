pipeline {
  environment {
    registry = 'registry.hub.docker.com'
    registryCredential = 'docker-hub'
    repository = 'pvnovarese/jenkins-syft-demo'
  }
  agent any
  stages {
    stage('Checkout SCM') {
      steps {
        checkout scm
      }
    }
    stage('Build image and push to registry') {
      steps {
        sh 'docker --version'
        script {
          docker.withRegistry('https://' + registry, registryCredential) {
            def image = docker.build(repository)
            //image.push()
          }
        }
      }
    }
    stage('Analyze with syft') {
      steps {
        // need to actually test this out once I get linux binaries
        sh '/var/jenkins_home/syft ${repository}:latest | grep curl; EXIT=$?; if [ $EXIT -eq 1 ]; then echo "no match"; exit 0; else echo "match found"; exit 1; fi'
      }
    }
    stage('Build and push prod image to registry') {
      steps {
        script {
          docker.withRegistry('https://' + registry, registryCredential) {
            def image = docker.build(repository + ':prod')
            image.push()  
          }
        }
      }
    }
  }
}

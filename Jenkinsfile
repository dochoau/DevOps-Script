pipeline {
  agent any

  options {
    timeout(time: 2, unit: 'MINUTES')
  }

  environment {
    ARTIFACT_ID = "webapp:${env.BUILD_NUMBER}"
    DOCKERHUB_CREDENTIALS = credentials('DockerHubCredentials')
    TAGGED_VERSION = "dochoau93/webapp"
  }

  stages {
    stage('Build') {
      steps {
        script {
          dir("") {
            dockerImage = docker.build "${env.ARTIFACT_ID}"
          }
        }
      }
    }
    stage('Run tests') {
      steps {
        sh "docker run ${dockerImage.id} npm test"
      }
    }
    stage('Publish') {
      when {
        branch 'master'
      }
      steps {
      	sh "echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin"
	      sh "docker tag ${env.ARTIFACT_ID} ${env.TAGGED_VERSION}"
	      sh "docker push ${env.TAGGED_VERSION}"
      }
    }
    stage('Schedule Staging Deployment') {
      when {
        branch 'master'
      }
      steps {
        build job: 'deploy-web-app-staging', parameters: [string(name: 'ARTIFACT_ID', value: "${env.ARTIFACT_ID}")], wait: false
      }
    }
  }
}
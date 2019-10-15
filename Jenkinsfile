pipeline {
  agent any
  stages {
	  stage('prebuild') {
			steps {
				withCredentials([file(credentialsId: 'jenkins-service-account-python-app', variable: 'jenkinsFlask')]) {
          sh """
            gcloud auth activate-service-account --key-file $jenkinsFlask && \
            gcloud config set compute/zone us-east1-b && \
            gcloud config set project python-app-255507 && \
            gcloud container clusters get-credentials flask-cluster && \
            kubectl get pods
          """
        }
			}
		}
    stage('build') {
      agent {
        docker {
          image 'anasabdullah/python-app:0.0.1'
          label 'anasabdullah/python-app:0.0.1'
          registryUrl 'https://index.docker.io/v1/'
          registryCredentialsId 'dockerhub'
        }
      } 
      steps {
	      sh """
          docker build -t anasabdullah/python-app:0.0.1 .
        """
      }
    }
  }
  post { 
    always { 
      cleanWs()
    }
  }
}

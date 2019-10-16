pipeline {
	environment {
    version = ""
  }
  agent any
  stages {
	  stage('prebuild') {
			steps {
        version = ${env.GIT_COMMIT}
        sh "echo ${version}"
				withCredentials([file(credentialsId: 'jenkins-service-account-python-app', variable: 'jenkinsFlask')]) {
          sh """
            gcloud auth activate-service-account --key-file $jenkinsFlask && \
            gcloud config set compute/zone us-east1-b && \
            gcloud config set project python-app-255507 && \
            gcloud container clusters get-credentials flask-cluster && \
          """
        }
			}
		}
    stage('build') {
      steps {
				script {
					docker.withRegistry('https://index.docker.io/v1/', 'dockerhub') {
      			def app = docker.build("anasabdullah/python-app:${version}", '.').push()
    			}
				}
      }
    }
		stage('deploy') {
			steps {
        script {
          try {
            sh "helm list"
          }
          catch(all) {
            sh "echo 'deployment failed'"
          }
        }
			}
		}
  }
  post { 
    always { 
      cleanWs()
    }
  }
}

pipeline {
	environment {
    version = ""
  }
  agent any
  stages {
	  stage('prebuild') {
			steps {
        script {
          version = sh(returnStdout: true, script: "git log -n 1 --pretty=format:'%h'").trim()
        }
				withCredentials([file(credentialsId: 'jenkins-service-account-python-app', variable: 'jenkinsFlask')]) {
          sh """
            gcloud auth activate-service-account --key-file $jenkinsFlask && \
            gcloud config set compute/zone us-east1-b && \
            gcloud config set project python-app-255507 && \
            gcloud container clusters get-credentials flask-cluster
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
            sh "helm upgrade flask-release kubernetes/ --reuse-values --set-string PYTHON_IMAGE=anasabdullah/python-app:${version}"
            sh "DEPLOYMENT_STATUS=`helm status flask-release | grep STATUS | awk '{split(${0},a," "); print a[2]'` && echo ${DEPLOYMENT_STATUS}"
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

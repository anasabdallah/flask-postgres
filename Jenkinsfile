pipeline {
	environment {
    VERSION = ""
  }
  agent any
  stages {
	  stage('prebuild') {
			steps {
        script {
          VERSION = sh(returnStdout: true, script: "git log -n 1 --pretty=format:'%h'").trim()
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
      			def app = docker.build("anasabdullah/python-app:${VERSION}", '.').push()
    			}
				}
      }
    }
		stage('deploy') {
			steps {
        script {
          try {
            sh "helm upgrade flask-release kubernetes/ --reuse-values --set-string PYTHON_IMAGE=anasabdullah/python-app:${VERSION}"
            String DEPLOYMENT_STATUS = sh(returnStdout: true, script: "helm status flask-release | grep STATUS: | awk '{split(\$0,a,\" \"); print a[2]}'")
            String REQUIRED_STATUS = "DEPLOYED"
            if ( DEPLOYMENT_STATUS == REQUIRED_STATUS ) { sh "exit 1" }
            echo "service deployed successfully ..."
            currentBuild.result = 'SUCCESS'
          }
          catch(all) {
            echo "deployment failed, rolling back .."
            sh "helm rollback flask-release 0"
            currentBuild.result = 'FAILURE'
          }
        }
			}
		}
  }
  post { 
    always {
      sh 'docker image prune -a --force --filter "until=48h"'
      cleanWs()
    }
  }
}

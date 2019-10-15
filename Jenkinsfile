pipeline {
  agent any 
  stages {
    stage('clean') {
      steps {
        sh 'ls -la'
				cleanWs()
      }
    }
	  stage('prebuild') {
			steps {
				sh 'ls -la'
			}
		}
  }
}

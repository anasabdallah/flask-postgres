pipeline {
    agent any 
    stages {
        stage('clean') {
            steps {
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

node {
  def version = '0.0.1'
  stage('clean') {
    cleanWs()
  }
  stage('prebuild') {
    sh "git clone https://github.com/anasabdallah/flask-postgres.git && mv flask-postgres/* ./ && rm -rf flask-postgres"
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
  stage('build') {
    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub') {
      def app = docker.build("anasabdullah/python-app:${version}", '.').push()
    }
  }
  stage('deploy') {
    sh """
       helm delete --purge famous-billygoat && \
       sleep 10 && \
       helm install kubernetes/ --name flask-release
       """
  }
}


node('frontservices-builder') {
  currentBuild.result = "SUCCESS"
  try {
    stage("checkout"){
      checkout scm
    }
    withCredentials([string(credentialsId: 'cloud-storage-admin', variable: 'SVC_ACCOUNT_KEY')]) {
          withEnv(['PATH+GCLOUDPATH=/root/google-cloud-sdk/bin','PRODUCT=spike','PROD_VERSION=latest']) {
            stage("jmeter-test-build") {
              container("builder") {
                  sh 'echo $SVC_ACCOUNT_KEY | base64 -d > gcloud-svc-acct.json'
                  sh 'gcloud auth activate-service-account --key-file gcloud-svc-acct.json'
                  sh 'gcloud config set project newswhip-224410'
                  sh 'gcloud auth configure-docker -q'
                  sh 'git clone git@github.com:NewsWhip/distributed-jmeter-docker.git'
                  sh 'git checkout newImageFIxes'
                  sh 'cd /distributed-jmeter-docker/'
                  sh "docker build -t dis-jmeter ."
                  sh "docker tag dis-jmeter gcr.io/newswhip-224410/dis-jmeter:5.0"
                  sh "docker push gcr.io/newswhip-224410/dis-jmeter:5.0"
              }
            }
          }
        }
  } catch (err) {
    currentBuild.result = "FAILURE"
    throw err
  }
}




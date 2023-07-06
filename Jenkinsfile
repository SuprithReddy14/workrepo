import groovy.json.JsonSlurper

def getFtpPublishProfile(def publishProfilesJson) {
  def pubProfiles = new JsonSlurper().parseText(publishProfilesJson)
  for (p in pubProfiles)
    if (p['publishMethod'] == 'FTP')
      return [url: p.publishUrl, username: p.userName, password: p.userPWD]
}

node {
  withEnv(['AZURE_SUBSCRIPTION_ID=901fda41-8b6f-4d44-8582-c5929e78a528',
        'AZURE_TENANT_ID=32e2117d-6fa4-4496-a8f1-d476ba629d95']) {
    stage('init') {
      checkout scm
    }
  
    stage('build') {
      sh 'mvn clean package'
    }
  
    stage('deploy') {
      def resourceGroup = 'newone'
      def webAppName = 'ballthunder'
      // login Azure
      withCredentials([usernamePassword(credentialsId: 'admin', passwordVariable: 'Edt8Q~-Bm44pX5I-zAd-XsLRkf1_J-PjnvaPGduI', usernameVariable: '8efcefff-247a-4b09-8f1e-c794a829c230')]) {
       sh '''
          az login --service-principal -u 8efcefff-247a-4b09-8f1e-c794a829c230 -p Edt8Q~-Bm44pX5I-zAd-XsLRkf1_J-PjnvaPGduI -t 32e2117d-6fa4-4496-a8f1-d476ba629d95
          az account set -s 901fda41-8b6f-4d44-8582-c5929e78a528
        '''
      }
      // get publish settings
      def pubProfilesJson = sh script: "az webapp deployment list-publishing-profiles -g newone -n ballthunder", returnStdout: true
      def ftpProfile = getFtpPublishProfile pubProfilesJson
      // upload package
      sh "curl -T target/calculator-1.0.war $ftpProfile.url/webapps/ROOT.war -u '$ftpProfile.username:$ftpProfile.password'"
      // log out
      sh 'az logout'
    }
  }
}

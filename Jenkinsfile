pipeline {
  agent any
  environment {
    CLOUDSDK_CORE_PROJECT='credenciales-364703'
    CLIENT_EMAIL='jenkins@insights-api-localdev.iam.gserviceaccount.com'
    GCLOUD_CREDS=credentials('GCP_SECRET')
    GCLOUD_PATH='C:\\Users\\aevar\\AppData\\Local\\Google\\Cloud SDK\\google-cloud-sdk\\bin'
  }
  stages {
    stage('Verificar gcloud') {
      steps {
        bat '"%GCLOUD_PATH%\\gcloud.cmd" --version'
      }
    }    
    stage('Verify version') {
      steps {
        bat '''
          "%GCLOUD_PATH%\\gcloud.cmd" version
        '''
      }
    }
    withCredentials([file(credentialsId: 'GCP_SECRET', variable: 'GCP_SECRET')]) {
        bat '''
          "%GCLOUD_PATH%\\gcloud.cmd" auth activate-service-account --key-file="$GCLOUD_CREDS"
        '''
    }    
    stage('Authenticate') {
      steps {
          withCredentials([file(credentialsId: 'GCP_SECRET', variable: 'GCP_SECRET')]) {
              bat '''
                "%GCLOUD_PATH%\\gcloud.cmd" auth activate-service-account --key-file="$GCP_SECRET"
              '''
          }          
        // bat '''
        //   "%GCLOUD_PATH%\\gcloud.cmd" auth activate-service-account --key-file="$GCLOUD_CREDS"
        // '''
      }
    }
    stage('Install service') {
      steps {
        bat '''
          "%GCLOUD_PATH%\\gcloud.cmd" run services replace service.yaml --platform='managed' --region='us-central1'
        '''
      }
    }
    stage('Allow allUsers') {
      steps {
        bat '''
          "%GCLOUD_PATH%\\gcloud.cmd" run services add-iam-policy-binding hello --region='us-central1' --member='allUsers' --role='roles/run.invoker'
        '''
      }
    }
  }
  post {
    always {
      bat '"%GCLOUD_PATH%\\gcloud.cmd" auth revoke %CLIENT_EMAIL%'
    }
  }
}
pipeline {
  agent {
    docker {
      image 'lukaszczerpak/herotools:latest'
    }

  }
  stages {
    stage('Build') {
      steps {
        sh '''mkdir -p output
              generate-prc-rules endpoints/ output/policy.json'''
        archiveArtifacts 'output/*.json'
      }
    }

    stage('Test') {
      steps {
        sh 'cloudlet-validate-policy --schema schema/create-nimbus_policy_version-CD-1.0.json output/policy.json'
      }
    }

    stage('Deploy') {
      steps {
        sh 'cloudlet-policy-update --policyid $POLICY_ID --policy output/policy.json --description "Commit: $(git rev-parse HEAD)"'
      }
    }

    stage('Release') {
      steps {
        sh 'cloudlet-policy-activate --policyid $POLICY_ID --network staging'
      }
    }

  }
  environment {
    POLICY_ID = '190112'
    AKAMAI_CLIENT_TOKEN = 'akab-v7juchl7xu2vauaf-ruiqisbi7ubd2er2'
    AKAMAI_CLIENT_SECRET = 'lCF/hmIILdzIjoxzaOUsydifXOtyxU4dFJ/LZj2y+E8='
    AKAMAI_ACCESS_TOKEN = 'akab-nc5za7hn3wlofab7-3pzo5jqxcqvis737'
    AKAMAI_HOST = 'akab-faicorz46bt5ji2l-gakc547teq3dgu7k.luna.akamaiapis.net'
  }
}
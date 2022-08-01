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
    POLICY_ID = '192295'
    AKAMAI_CLIENT_TOKEN = credentials('AKAU_AKAMAI_CLIENT_TOKEN')
    AKAMAI_CLIENT_SECRET = credentials('AKAU_AKAMAI_CLIENT_SECRET')
    AKAMAI_ACCESS_TOKEN = credentials('AKAU_AKAMAI_ACCESS_TOKEN')
    AKAMAI_HOST = credentials('AKAU_AKAMAI_HOST')
  }
}
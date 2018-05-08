#!groovy

properties(
  [[$class: 'GithubProjectProperty', projectUrlStr: 'https://github.com/hmcts/vault-token-role/'],
   pipelineTriggers([[$class: 'GitHubPushTrigger']])]
)

@Library('Reform') _

node {
  ws('vault-token-role') { 
    try {
      wrap([$class: 'AnsiColorBuildWrapper', colorMapName: 'xterm']) {
        stage('Checkout') {
          checkout scm
        }

        stage('Kill any existing jobs') {
          sh '''
          molecule destroy
        '''
        }

        stage('Start containers') {
          sh '''
          molecule create
        '''
        }

        stage('Run tests') {
          sh '''
          molecule syntax
          # molecule verify
        '''
        }

        stage('Run role') {
          sh '''
          molecule converge
        '''
        }

        // Role known not to be idempotent due to random passwords
//      stage('Check idempotent') {
//        sh '''
//        molecule idempotence
//      '''
//      }

      }

    } catch (err) {
      notifyBuildFailure channel: '#devops'
      throw err
    } finally {
      stage('Cleanup') {
        sh '''
         molecule destroy
        '''
      }
      deleteDir()
    }
  }
}

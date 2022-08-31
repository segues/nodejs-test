def coberturaFile = 'output/coverage/jest/cobertura-coverage.xml'
def repositoryUrl = scm.userRemoteConfigs[0].url

pipeline {
  agent {
    kubernetes {
      defaultContainer 'npm'
      inheritFrom 'npm'
    }
  }
  options {
    disableConcurrentBuilds()
    skipDefaultCheckout()
  }
  stages {
    stage ('Checkout') {
      steps {
        checkout scm
      }
    }
    stage('Test') {
      steps {
        sh 'npm run test'
        script {
          if (fileExists ("${coberturaFile}")) {
            publishCoverage adapters: [coberturaAdapter("${coberturaFile}")]
          }
        }
      }
    }
    stage('Publish Coverage') {
      when { expression { return fileExists ("${coberturaFile}") } }
      steps {
        script {
          currentBuild.result = 'SUCCESS'
        }
        script {
          if (env.BRANCH_NAME == 'master') {
            step([$class: 'MasterCoverageAction', scmVars: [GIT_URL: repositoryUrl]])
          } else {
            step([$class: 'CompareCoverageAction', publishResultAs: 'statusCheck', scmVars: [GIT_URL: repositoryUrl]])
          }
        }
      }
    }
    
  }
}

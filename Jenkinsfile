#!groovy

properties(
        [[$class: 'GithubProjectProperty', displayName: 'IdAM BOM', projectUrlStr: 'https://git.reform.hmcts.net/idam/idam-bom/'],
         pipelineTriggers([[$class: 'GitHubPushTrigger']])]
)

@Library('Reform') _

def channel = '#idam_tech'

timestamps {
   lock(resource: "idam-bom-${env.BRANCH_NAME}", inversePrecedence: true) {
        node {
            try {
                stage('Checkout') {
                    deleteDir()
                    checkout scm
                }
                def mvnHome
                mvnHome = tool 'apache-maven-3.5.2'
                stage('Build') {
                    onMaster {
                        sh "'${mvnHome}/bin/mvn' clean deploy"
                    }
                    onPR {
                        sh "'${mvnHome}/bin/mvn' clean package"
                    }
                }
            } catch (err) {
                notifyBuildFailure channel: channel
                throw err
            }

        }
        milestone()
    }

    notifyBuildFixed channel: channel
}

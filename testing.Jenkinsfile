pipeline {
    agent any
    triggers {
        pollSCM('H * * * *')
    }
    stages {
        stage('Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/master']],
                    doGenerateSubmoduleConfigurations: false,
                    extensions: [
                        [$class: 'PathRestriction', includedRegions: 'test-1/.*']
                    ],
                    userRemoteConfigs: [[
                        url: 'https://github.com/deepanshu-rawat6/jenkins-test-repo.git'
                    ]],
                ])
            }
        }
        stage('Check Changes in ServiceA Folder') {
            steps {
                script {
                    sh 'git fetch --all' // ensure local is updated
                    def changes = sh(returnStdout: true, script: 'git diff --name-only origin/master HEAD -- test-1/').trim()
                    if (!changes) {
                        // No changes in ServiceA folder
                        echo "No changes in test-1 folder, skipping build."
                        currentBuild.result = 'NOT_BUILT'
                        error("Stopping the pipeline.")
                    }
                }
            }
        }
        stage('Build Service A') {
            steps {
                echo "Building Service A..."
                // build steps
            }
        }
    }
}
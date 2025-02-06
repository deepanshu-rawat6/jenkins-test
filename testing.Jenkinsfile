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
                    branches: [[name: '*/main']],
                    doGenerateSubmoduleConfigurations: false,
                    extensions: [
                        [$class: 'PathRestriction', includedRegions: 'ServiceA/.*']
                    ],
                    userRemoteConfigs: [[
                        credentialsId: 'your-git-creds',
                        url: 'git@github.com:your-org/repo1.git'
                    ]]
                ])
            }
        }
        stage('Check Changes in ServiceA Folder') {
            steps {
                script {
                    sh 'git fetch --all' // ensure local is updated
                    def changes = sh(returnStdout: true, script: 'git diff --name-only origin/main HEAD -- test-1/').trim()
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
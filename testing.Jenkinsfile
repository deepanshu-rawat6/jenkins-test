pipeline {
    agent any
    
    // 1) Use Poll SCM to check for changes in octo every X minutes/hours
    triggers {
        pollSCM('* * * * *')   // e.g. every hour
    }
    
    stages {
        stage('Checkout Correlation Code') {
            steps {
                // 2) We point to 'octo' Git repo (Repo-A), not Repo-B
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/master']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/deepanshu-rawat6/jenkins-test-repo'
                    ]],
                    extensions: [
                        [$class: 'PathRestriction', includedRegions: 'test-1/.*', excludedRegions: '']
                    ]
                ])
            }
        }
        
        stage('Build & Push Image') {
            steps {
                // This stage runs only if there's a change in the Correlation folder
                echo "Building and pushing Docker image for Correlation..."
                // Build steps here...
            }
        }
    }
}
def hasChangesInFolder(folderPath) {
    script {
        dir("${env.WORKSPACE}") { // Ensure it's checking from repo root
            def lastCommit = sh(script: "git rev-parse origin/master", returnStdout: true).trim()
            def previousCommit = sh(script: "git rev-parse origin/master@{1}", returnStdout: true).trim()

            echo "Checking changes from ${previousCommit} to ${lastCommit}"

            def changedFiles = sh(script: "git diff --name-only ${previousCommit} ${lastCommit}", returnStdout: true).trim()

            echo "Changed files: ${changedFiles}"

            if (changedFiles.contains(folderPath)) {
                echo "✅ Changes detected in ${folderPath}. Proceeding with the build..."
                return true
            } else {
                echo "❌ No changes detected in ${folderPath}. Failing the pipeline!"
                error("Build failed due to no changes in ${folderPath}.")
            }
        }
    }
}

pipeline {
    agent any
    
    // 1) Use Poll SCM to check for changes in octo every X minutes/hours
    // triggers {
    //     pollSCM('* * * * *')   // e.g. every hour
    // }

    // triggers {
    //     cron('* * * * *')  // Runs every minute
    // }
    
    stages {
        stage('Checkout Correlation Code') {
            
            steps {
                // 2) We point to 'octo' Git repo (Repo-A), not Repo-B
                // checkout([
                //     $class: 'GitSCM',
                //     branches: [[name: '*/master']],
                //     userRemoteConfigs: [[
                //         url: 'https://github.com/deepanshu-rawat6/jenkins-test-repo'
                //     ]],
                //     extensions: [
                //         [$class: 'PathRestriction', includedRegions: 'test-1/.*', excludedRegions: '']
                //     ]
                // ])

                        dir('test-1'){
                            script {
                                properties([pipelineTriggers([pollSCM('* * * * *')])])
                            }
                            // checkout([
                            //     $class: 'GitSCM',
                            //     branches: [[name: '*/master']],
                            //     doGenerateSubmoduleConfigurations: false,
                            //     extensions: [
                            //         [$class: 'PathRestriction', includedRegions: 'test-1/.*', excludedRegions: '']
                            //     ],
                            //     userRemoteConfigs: [[url: 'https://github.com/deepanshu-rawat6/jenkins-test-repo']]
                            // ])
                        }
            }
        }

        stage('Checking for Correlation changes') {
            steps {
                script {
                    hasChangesInFolder('test-1/')
                }
            }
        }

        stage('Build & Push Image') {
            steps {
                // This stage runs only if there's a change in the Correlation folder
                sh '''
                    echo "Building and pushing Docker image for Correlation..."
                    pwd
                    ls -la
                    cd test-1
                    ls -la
                '''
                // Build steps here...
            }
        }
    }
}
pipeline {
    agent any
    
    // 1) Use Poll SCM to check for changes in octo every X minutes/hours
    // triggers {
    //     pollSCM('* * * * *')   // e.g. every hour
    // }

    triggers {
        // Poll only for changes in the Classification folder (if supported by your Jenkins setup)
        pollSCM(
            scmpoll_spec: 'H * * * *',
            extensions: [[$class: 'PathRestriction', includedRegions: 'test-1/.*']]
        )
    }
    
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


                        // dir('test-1'){
                        //     script {
                        //         properties([pipelineTriggers([pollSCM('* * * * *')])])
                        //     }
                        //     checkout([
                        //         $class: 'GitSCM',
                        //         branches: [[name: '*/master']],
                        //         doGenerateSubmoduleConfigurations: false,
                        //         extensions: [
                        //             [$class: 'PathRestriction', includedRegions: 'test-1/.*', excludedRegions: '']
                        //         ],
                        //         userRemoteConfigs: [[url: 'https://github.com/deepanshu-rawat6/jenkins-test-repo']]
                        //     ])
                        // }
                        dir('test-1'){
                            script {
                                properties([pipelineTriggers([pollSCM('* * * * *')])])
                            }
                            checkout([
                                $class: 'GitSCM',
                                branches: [[name: '*/master']],
                                doGenerateSubmoduleConfigurations: false,
                                userRemoteConfigs: [[url: 'https://github.com/deepanshu-rawat6/jenkins-test-repo']]
                            ])
                        }
            }
        }
        
        stage('Build & Push Image') {
            steps {
                // This stage runs only if there's a change in the Correlation folder
                sh '''
                    echo "Building and pushing Docker image for Correlation..."
                    pwd
                '''
                // Build steps here...
            }
        }
    }
}
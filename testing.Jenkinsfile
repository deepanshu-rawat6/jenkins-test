pipeline {
    agent any
    
    // 1) Use Poll SCM to check for changes in octo every X minutes/hours
    // triggers {
    //     pollSCM('* * * * *')   // e.g. every hour
    // }

    triggers {
        cron('* * * * *')  // Runs every minute
    }
    
    stages {
        // stage('Checkout Correlation Code') {
            
        //     steps {
        //         // 2) We point to 'octo' Git repo (Repo-A), not Repo-B
        //         // checkout([
        //         //     $class: 'GitSCM',
        //         //     branches: [[name: '*/master']],
        //         //     userRemoteConfigs: [[
        //         //         url: 'https://github.com/deepanshu-rawat6/jenkins-test-repo'
        //         //     ]],
        //         //     extensions: [
        //         //         [$class: 'PathRestriction', includedRegions: 'test-1/.*', excludedRegions: '']
        //         //     ]
        //         // ])


        //                 dir('test-1'){
        //                     script {
        //                         properties([pipelineTriggers([pollSCM('* * * * *')])])
        //                     }
        //                     checkout([
        //                         $class: 'GitSCM',
        //                         branches: [[name: '*/master']],
        //                         doGenerateSubmoduleConfigurations: false,
        //                         extensions: [
        //                             [$class: 'PathRestriction', includedRegions: 'test-1/.*', excludedRegions: '']
        //                         ],
        //                         userRemoteConfigs: [[url: 'https://github.com/deepanshu-rawat6/jenkins-test-repo']]
        //                     ])
        //                 }
        //     }
        // }

        stage('Check for Changes in jenkins-test-repo Repo') {
            steps {
                script {
                    def repoUrl = "https://github.com/deepanshu-rawat6/jenkins-test-repo"
                    def branch = "master"
                    def targetDirectory = "test-1"

                    sh """
                        rm -rf ${targetDirectory}
                        git clone --depth=1 --branch ${branch} ${repoUrl} ${targetDirectory}
                        cd ${targetDirectory}
                        git fetch origin ${branch}
                        
                        if git diff --name-only HEAD@{1} HEAD | grep '^test-1/' > /dev/null; then
                            echo "Changes detected in test-1/, proceeding with build."
                            echo "CHANGES_FOUND=true" >> \$WORKSPACE/build.env
                        else
                            echo "No changes in test-1/, skipping build."
                            echo "CHANGES_FOUND=false" >> \$WORKSPACE/build.env
                        fi
                    """
                }
            }
        }

        stage('Clone test Repo') {
            when {
                expression {
                    def buildEnv = readFile("${env.WORKSPACE}/build.env").trim()
                    return buildEnv.contains("CHANGES_FOUND=true")
                }
            }
            steps {
                dir('jenkins-test') {
                    checkout([
                        $class: 'GitSCM',
                        branches: ["master"],
                        doGenerateSubmoduleConfigurations: false,
                        extensions: [
                            [$class: 'PathRestriction', includedRegions: 'test-1/.*', excludedRegions: '']
                        ],
                        userRemoteConfigs: [[credentialsId: 'Github', url: 'https://github.com/deepanshu-rawat6/jenkins-test-repo']]
                    ])
                }
            }
        }
        
        stage('Build & Push Image') {
            when {
                expression {
                    def buildEnv = readFile("${env.WORKSPACE}/build.env").trim()
                    return buildEnv.contains("CHANGES_FOUND=true")
                }
            }
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
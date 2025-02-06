pipeline {
    agent any

    triggers {
        // Poll SCM every
        pollSCM('* * * * *')
    }

    stages {
        stage('Checkout Specific Folder') {
            steps {

                dir('Repo-1') {
                    script {
                            properties([pipelineTriggers([pollSCM('H * * * *')])])
                        }
                        git branch: master, url: 'https://github.com/deepanshu-rawat6/jenkins-test-repo'
                }
                // This ensures only changes in folder1 will trigger 
                // (adjust includedRegions for job1 vs. job2)
                // checkout([
                //     $class: 'GitSCM',
                //     branches: [[name: '*/master']],
                //     userRemoteConfigs: [[
                //         url: 'https://github.com/deepanshu-rawat6/jenkins-test-repo.git'
                //     ]],
                //     extensions: [
                //         [$class: 'PathRestriction',
                //         includedRegions: 'test-1/.*', 
                //         excludedRegions: 'test-2/.*']
                //     ]
                // ])
            }
        }
        stage('Build/Deploy') {
            steps {
                // Run build for the code in folder1
                sh """
                  cd test-1
                  ls -l
                  # ... your build steps for service A
                """
            }
        }
    }
}
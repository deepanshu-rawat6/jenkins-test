pipeline {
    agent any

    triggers {
        // Poll SCM every minute
        pollSCM('* * * * *')
    }

    stages {
        stage('Checkout Specific Folder') {
            steps {
                // This ensures only changes in folder1 will trigger 
                // (adjust includedRegions for job1 vs. job2)
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/master']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/deepanshu-rawat6/jenkins-test-repo.git'
                    ]],
                    extensions: [
                        [$class: 'PathRestriction',
                        includedRegions: 'test-2/.*', 
                        excludedRegions: 'test-1/.*']
                    ]
                ])
            }
        }
        stage('Build/Deploy') {
            steps {
                // Run build for the code in folder1
                sh """
                  cd test-2
                  ls -l
                  # ... your build steps for service A
                """
            }
        }
    }
}
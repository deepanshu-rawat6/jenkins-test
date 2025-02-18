pipeline {
    agent any
    
    triggers {
        pollSCM('* * * * *')
    }

    environment {
        REPO_A_URL = 'https://github.com/deepanshu-rawat6/jenkins-test-repo.git'
        REPO_A_DIR = 'jenkins-test-repo'
    }

    stages {
        stage('Checkout jenkins-test-repo') {
            steps {
                script {
                    // Clone jenkins-test-repo into a separate directory
                    checkout([
                        $class: 'GitSCM',
                        branches: [[name: '*/master']],
                        userRemoteConfigs: [[url: env.REPO_A_URL]],
                        extensions: [[$class: 'RelativeTargetDirectory', relativeTargetDir: env.REPO_A_DIR]]
                    ])
                }
            }
        }

        stage('Check for Changes in test-1/ or test-2/') {
            steps {
                script {
                    def test1Changed = hasChangesInFolder("${env.REPO_A_DIR}/test-1/")
                    def test2Changed = hasChangesInFolder("${env.REPO_A_DIR}/test-2/")

                    if (!test1Changed && !test2Changed) {
                        echo "❌ No relevant changes detected in jenkins-test-repo. Skipping build!"
                        currentBuild.result = 'ABORTED'
                        return
                    }

                    if (test1Changed) {
                        echo "🚀 Changes detected in test-1/. Running Jenkinsfile-1..."
                        build(job: 'testing-Jenkinsfile')  // Triggers Jenkinsfile-1 job
                    }

                    if (test2Changed) {
                        echo "🚀 Changes detected in test-2/. Running Jenkinsfile-2..."
                        build(job: 'testing2.Jenkinsfile')  // Triggers Jenkinsfile-2 job
                    }
                }
            }
        }
    }
}

def hasChangesInFolder(folderPath) {
    script {
        dir(env.REPO_A_DIR) {  // Go to jenkins-test-repo directory
            def lastCommit = sh(script: "git rev-parse origin/master", returnStdout: true).trim()
            def previousCommit = sh(script: "git rev-parse origin/master@{1}", returnStdout: true).trim()

            echo "Checking changes from ${previousCommit} to ${lastCommit} in ${folderPath}"

            def changedFiles = sh(script: "git diff --name-only ${previousCommit} ${lastCommit}", returnStdout: true).trim()

            echo "Changed files: ${changedFiles}"

            // Check if any file inside the given folder path is changed
            return changedFiles.split('\n').any { it.startsWith(folderPath.replace("${env.REPO_A_DIR}/", "")) }
        }
    }
}
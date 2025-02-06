def hasChangesInFolder(String folderPath) {
    def previousCommit = sh(script: "git rev-parse HEAD@{1}", returnStdout: true).trim()
    def currentCommit = sh(script: "git rev-parse HEAD", returnStdout: true).trim()
    
    def changes = sh(
        script: "git diff --name-only ${previousCommit} ${currentCommit} | grep '^${folderPath}/' || true",
        returnStdout: true
    ).trim()
    
    return changes != ''
}

pipeline {
    agent any 

    triggers {
        cron('* * * * *') // Hourly trigger
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/deepanshu-rawat6/jenkins-test-repo.git'
            }
        }
        
        stage('Job 1 - Folder 1') {
            when {
                expression { hasChangesInFolder('test-1') }
            }
            steps {
                echo "Running Job 1 for Folder 1 changes"
                sh 'cat test-1/README.md'
                // Your job 1 steps
            }
        }
        
        stage('Job 2 - Folder 2') {
            when {
                expression { hasChangesInFolder('test-2') }
            }
            steps {
                echo "Running Job 2 for Folder 2 changes"
                sh 'cat test-2/README.md'
                // Your job 2 steps
            }
        }
    }
}
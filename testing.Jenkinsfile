def checkFolderChanges(String folderPath) {
    def owner = 'deepanshu-rawat6'
    def repo = 'jenkins-test-repo'
    
    withCredentials([string(credentialsId: 'GITHUB_TOKEN', variable: 'TOKEN')]) {
        def response = sh(
            script: """
                curl -s -H "Authorization: token ${TOKEN}" \
                https://api.github.com/repos/${owner}/${repo}/commits
            """,
            returnStdout: true
        )
        
        def commits = readJSON text: response
        def lastBuildTime = currentBuild.previousBuild?.timeInMillis ?: 0
        
        for (commit in commits) {
            def commitTime = Date.parse("yyyy-MM-dd'T'HH:mm:ss'Z'", commit.commit.author.date).time
            if (commitTime > lastBuildTime) {
                def changedFiles = sh(
                    script: """
                        curl -s -H "Authorization: token ${TOKEN}" \
                        ${commit.url}
                    """,
                    returnStdout: true
                )
                def commitData = readJSON text: changedFiles
                if (commitData.files.any { it.filename.startsWith(folderPath) }) {
                    return true
                }
            }
        }
    }
    return false
}

pipeline {
    agent any
    
    triggers {
        cron('H * * * *')
    }
    
    stages {
        stage('Checkout') {
            steps {
                script {
                    // Clean approach: Remove and clone fresh
                    sh '''
                        if [ -d "jenkins-test-repo" ]; then
                            rm -rf jenkins-test-repo
                        fi
                        git clone https://github.com/deepanshu-rawat6/jenkins-test-repo.git
                        cd jenkins-test-repo
                        # Get the default branch name
                        DEFAULT_BRANCH=$(git remote show origin | grep 'HEAD branch' | cut -d' ' -f5)
                        git checkout $DEFAULT_BRANCH
                    '''
                    
                    env.FOLDER1_CHANGED = checkFolderChanges('test-1').toString()
                    env.FOLDER2_CHANGED = checkFolderChanges('test-2').toString()
                }
            }
        }
        
        stage('Job 1 - Folder 1') {
            when {
                expression { return env.FOLDER1_CHANGED == 'true' }
            }
            steps {
                echo "Running Job 1 for Folder 1 changes"
                sh 'cat jenkins-test-repo/test-1/README.md'
            }
        }
        
        stage('Job 2 - Folder 2') {
            when {
                expression { return env.FOLDER2_CHANGED == 'true' }
            }
            steps {
                echo "Running Job 2 for Folder 2 changes"
                sh 'cat jenkins-test-repo/test-2/README.md'
            }
        }
    }
}
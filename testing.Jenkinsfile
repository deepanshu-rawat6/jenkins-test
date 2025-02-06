import groovy.json.JsonSlurper

def checkFolderChanges(String folderPath) {
    def owner = 'deepanshu-rawat6'
    def repo = 'jenkins-test-repo'
    
    withCredentials([string(credentialsId: 'GITHUB_TOKEN', variable: 'TOKEN')]) {
        def response = httpRequest(
            url: "https://api.github.com/repos/${owner}/${repo}/commits",
            customHeaders: [[name: 'Authorization', value: "token ${TOKEN}"]],
            validResponseCodes: '200'
        )
        
        def commits = new JsonSlurper().parseText(response.content)
        def lastBuildTime = currentBuild.previousBuild?.timeInMillis ?: 0

        for (commit in commits) {
            def commitTime = Date.parse("yyyy-MM-dd'T'HH:mm:ss'Z'", commit.commit.author.date).time
            if (commitTime > lastBuildTime) {
                def changedFiles = httpRequest(
                    url: commit.url,
                    customHeaders: [[name: 'Authorization', value: "token ${TOKEN}"]],
                    validResponseCodes: '200'
                )
                def commitData = new JsonSlurper().parseText(changedFiles.content)
                
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
        cron('* * * * *') // Run every hour
    }
    
    stages {
        stage('Checkout') {
            steps {
                script {
                    sh '''
                        if [ -d "jenkins-test-repo" ]; then
                            rm -rf jenkins-test-repo
                        fi
                        git clone https://github.com/deepanshu-rawat6/jenkins-test-repo.git
                        cd jenkins-test-repo
                        
                        # Get the default branch name
                        DEFAULT_BRANCH=$(git remote show origin | grep 'HEAD branch' | cut -d' ' -f5)
                        
                        echo "Default branch: $DEFAULT_BRANCH"
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
                sh 'cat test-1/README.md'
            }
        }
        
        stage('Job 2 - Folder 2') {
            when {
                expression { return env.FOLDER2_CHANGED == 'true' }
            }
            steps {
                echo "Running Job 2 for Folder 2 changes"
                sh 'cat test-2/README.md'
            }
        }
    }
}
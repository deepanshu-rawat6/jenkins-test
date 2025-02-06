def checkFolderChanges(String folderPath) {
    def owner = 'deepanshu-rawat6'
    def repo = 'jenkins-test-repo'
    
    withCredentials([string(credentialsId: 'GITHUB_TOKEN', variable: 'TOKEN')]) {
        def response = httpRequest(
            url: "https://api.github.com/repos/${owner}/${repo}/commits",
            headers: [[name: 'Authorization', value: "token ${TOKEN}"]],
            validResponseCodes: '200'
        )

        def commits = readJSON text: response.content
        def lastBuildTime = currentBuild.previousBuild?.timeInMillis ?: 0

        for (commit in commits) {
            def commitTime = Date.parse("yyyy-MM-dd'T'HH:mm:ss'Z'", commit.commit.author.date).time
            if (commitTime > lastBuildTime) {
                def changedFiles = httpRequest(
                    url: commit.url,
                    headers: [[name: 'Authorization', value: "token ${TOKEN}"]],
                    validResponseCodes: '200'
                )
                def commitData = readJSON text: changedFiles.content

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
        cron('* * * * *') // Hourly trigger
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/deepanshu-rawat6/jenkins-test-repo.git'
            }
            script {
                    env.FOLDER1_CHANGED = checkFolderChanges('test-1').toString()
                    env.FOLDER2_CHANGED = checkFolderChanges('test-2').toString()
                }
        }
        
        stage('Job 1 - Folder 1') {
            when {
                expression { return env.FOLDER1_CHANGED == 'true' }
            }
            steps {
                echo "Running Job 1 for Folder 1 changes"
                sh 'cat test-1/README.md'
                // Your job 1 steps
            }
        }
        
        stage('Job 2 - Folder 2') {
            when {
                expression { return env.FOLDER2_CHANGED == 'true' }
            }
            steps {
                echo "Running Job 2 for Folder 2 changes"
                sh 'cat test-2/README.md'
                // Your job 2 steps
            }
        }
    }
}
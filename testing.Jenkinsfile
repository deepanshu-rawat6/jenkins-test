pipeline {
    agent any 

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out code..'
            }
        }
        stage('Build-1') {
            when {
                changeset "**/test-1/*.*"
            }
            steps {
                sh 'cat test-1/README.md'
            }
        }
        stage ('Build-2') {
            when {
                changeset "**/test-2/*.*"
            }
            steps {
                sh 'cat test-2/README.md'
            }
        }
    }
}
pipeline {
    agent {
        node {
            label 'maven'
        }
    }
    
    stages {
        stage('clone-code') {
            steps {
                git 'https://github.com/Ashishwakde26/tweet-trend.git'
            }
        }
    }
}
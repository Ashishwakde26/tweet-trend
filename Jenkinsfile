pipeline {
    agent {
        node {
            label 'maven'
        }
    }
    environment {
        PATH = "/opt/apache-maven-3.9.11/bin:$PATH"

        dockerHubCredentialId = 'docker-cred'
        dockerHubUser = 'ashishwakde26'
        imageName = 'my-devops=app'
        imageTag = "${env.BUILD_NUMBER}"

    }



    stages {
         stage("build") {
            steps {
                sh 'mvn clean deploy -Dmaven.test.skip=true'
            }
        }
        stage('SonarQube analysis') {
        environment {
            scannerHome = tool 'ashish-sonar-scanner'
        }
        steps{
            withSonarQubeEnv('ashish-sonarqube-server') { // If you have configured more than one global server connection, you can specify its name
                sh "${scannerHome}/bin/sonar-scanner"
            }
        }
    }
    // --- 1. Build the Docker Image ---
        stage('Build Image') {
            steps {
                script {
                    echo "Building Docker image ${dockerHubUser}/${imageName}:${imageTag}"                    
                    sh "docker build -t ${dockerHubUser}/${imageName}:${imageTag} ."                    
                    echo "Docker image built successfully."
                }
            }
        }
    }

}

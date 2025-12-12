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
        imageName = 'my-devops-app'
        imageTag = "latest"

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
      //--- 1. Build the Docker Image ---
        stage('Build Image') {
            steps {
                script {
                    echo "Building Docker image ${dockerHubUser}/${imageName}:${imageTag}"                    
                    sh "docker build -t ${dockerHubUser}/${imageName}:${imageTag} ."                    
                    echo "Docker image built successfully."
                }
            }
        }

        // --- 2. Login to Docker Hub ---
        stage('Docker Login') {
            steps {
                // Use the 'withCredentials' block to securely access credentials
                withCredentials([usernamePassword(
                    credentialsId: dockerHubCredentialId,
                    passwordVariable: 'DOCKER_PASSWORD',
                    usernameVariable: 'DOCKER_USERNAME'
                )]) {
                    sh "echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USERNAME --password-stdin"
                }
            }
        }

        // --- 3. Push the Image to Docker Hub ---
        stage('Push Image') {
            steps {
                script {
                    def fullImageName = "${dockerHubUser}/${imageName}:${imageTag}"
                    echo "Pushing image ${fullImageName} to Docker Hub..."
                    
                    sh "docker push ${fullImageName}"
                    
                    echo "Image push complete. Image URL: https://hub.docker.com/r/${dockerHubUser}/${imageName}/tags"
                }
            }
        }

        // --- 4. (Optional) Cleanup ---
        stage('Cleanup') {
            steps {
                script {
                    // Log out of Docker Hub and remove local image to free space
                    sh "docker logout"
                    sh "docker rmi ${dockerHubUser}/${imageName}:${imageTag}"
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh './deploy.sh'
                }
            }
        }

        stage(" Deploy ") {
            steps {
                script {
                    echo '<--------------- Helm Deploy Started --------------->'
                    sh 'helm install ttrend ttrend-0.1.0.tgz'
                    echo '<--------------- Helm deploy Ends --------------->'
                }
            }
        }
    }

}

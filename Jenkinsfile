node {
    // SCM Checkout: Pulling the code from the specified GitHub repository
    stage('SCM Checkout') {
        git url: 'https://github.com/svpveeresha/java-web-app-docker.git', branch: 'master'  // Updated GitHub repo path
    }

    // Maven Clean Package: Build the project using Maven
    stage("Maven Clean Package") {
        // Use the Maven tool from Global Tool Configuration (Updated to Maven 3.9.9)
        def mavenHome = tool name: "Maven-3.9.9", type: "maven"
        def mavenCMD = "${mavenHome}/bin/mvn"
        sh "${mavenCMD} clean package"  // Maven clean and package command
    }

    // Build Docker Image: Create a Docker image from the application
    stage('Build Docker Image') {
        sh 'docker build -t veereshajlr/java-web-app .'  // Build the Docker image with your username
    }

    // Push Docker Image: Login to Docker Hub and push the image
    stage('Push Docker Image') {
        // Using the credentials directly with updated username and password
        sh "docker login -u veereshajlr -p Veeresha@8812345"  // Login to Docker Hub with updated username
        sh 'docker push veereshajlr/java-web-app'  // Push the image to your own Docker Hub repository
    }

    // Run Docker Image in Dev Server: Deploy the Docker container to the server
    stage('Run Docker Image In Dev Server') {
        def dockerRun = 'docker run -d -p 8080:8080 --name java-web-app veereshajlr/java-web-app'

        // Use SSH to manage Docker container on the remote server
        sshagent(['DOCKER_SERVER']) {
            // Stop the existing container (if running)
            sh 'ssh -o StrictHostKeyChecking=no ubuntu@172.31.20.72 docker stop java-web-app || true'
            
            // Remove the existing container
            sh 'ssh ubuntu@172.31.20.72 docker rm java-web-app || true'
            
            // Remove any images (force removal)
            sh 'ssh ubuntu@172.31.20.72 docker rmi -f $(docker images -q) || true'
            
            // Run the new Docker container
            sh "ssh ubuntu@172.31.20.72 ${dockerRun}"
        }
    }
}

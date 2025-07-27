pipeline {
    agent any

    tools {
        jdk 'jdk-17'
        maven 'maven-3.9.11'
    }

    environment {
        DOCKER_IMAGE = "mdizzath7373/taskmanager:latest"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh "mvn clean package -DskipTests"
            }
        }

        stage('Docker Build & Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: '4e5ea798-24b5-4268-9b13-9cdb1d691d0d', toolName: 'docker-24') {
                        sh "docker build -t $DOCKER_IMAGE ."
                        sh "docker push $DOCKER_IMAGE"
                    }
                }
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    sh '''
                        docker rm -f taskmanager || true
                        docker run -d --name taskmanager -p 8082:8080 $DOCKER_IMAGE
                    '''
                }
            }
        }
    }

    post {
        success {
            mail to: 'mohamedizzath2k@gmail.com',
                 subject: "Taskmanager Build & Deployment SUCCESS",
                 body: "The build and deployment were successful.\nApplication URL: http://<YOUR_SERVER_IP>:8080"
        }
        failure {
            mail to: 'mohamedizzath2k@gmail.com',
                 subject: "Taskmanager Build FAILED",
                 body: "The build or deployment failed. Please check Jenkins logs."
        }
    }
}

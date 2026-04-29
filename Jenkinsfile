pipeline {
    agent any

 tools {
        maven 'Maven-3'
    }
    environment {
        GITHUB_IMAGE = "ghcr.io/chandrashekar528/docker-demo"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/chandrashekar528/gitexample.git'
            }
        }

        stage('Build JAR') {
            steps {
                bat 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat """
                docker build -t ${GITHUB_IMAGE}:${IMAGE_TAG} .
                docker tag ${GITHUB_IMAGE}:${IMAGE_TAG} ${GITHUB_IMAGE}:latest
                """
            }
        }

        stage('Push to GitHub Container Registry') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: '74b841a5-7ac7-43ce-bcb1-eb2e0613d9be',
                    usernameVariable: 'GITHUB_USER',
                    passwordVariable: 'GITHUB_TOKEN'
                )]) {
                    bat """
                    echo $GITHUB_TOKEN | docker login ghcr.io -u $GITHUB_USER --password-stdin
                    docker push ${GITHUB_IMAGE}:${IMAGE_TAG}
                    docker push ${GITHUB_IMAGE}:latest
                    docker logout ghcr.io
                    """
                }
            }
        }
    }
}

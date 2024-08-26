pipeline {
    agent any
    environment {
        DOCKER_CREDENTIAL_ID = 'NEXUS_CRED' // Thay bằng ID credentials trong Jenkins để kết nối với Nexus
        NEXUS_URL = 'http://192.168.127.100:8081/' // Thay bằng URL của Nexus
        NEXUS_REPOSITORY = 'wordpress' // Thay bằng tên repository Docker trên Nexus
    }
    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/Phuc03hp/wordpress_image.git' // Thay bằng URL repository của bạn
            }
        }
        stage('Build and Tag Docker Image') {
            steps {
                script {
                    dockerImage = "wordpress_phuc:${env.BUILD_NUMBER}"
                    sh "docker build -t ${dockerImage} ." // Build Docker image sử dụng Dockerfile
                }
            }
        }
        stage('Push Docker Image to Nexus') {
            steps {
                script {
                    docker.withRegistry("${NEXUS_URL}/repository/${NEXUS_REPOSITORY}", "${DOCKER_CREDENTIAL_ID}") {
                        sh "docker push ${dockerImage}"
                    }
                }
            }
        }
        stage('Clean Up') {
            steps {
                script {
                    sh "docker rmi ${dockerImage}"
                }
            }
        }
    }
}


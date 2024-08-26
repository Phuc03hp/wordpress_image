pipeline {

    agent any

    environment {
        imageName = "mywordpress"
        registryCredentials = "NEXUS_CRED"
        registry = "192.168.127.100:5000"
        dockerImage = ''
    }

    stages {
        stage('Code checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/Phuc03hp/wordpress_image.git']]])
            }
        }

        stage('Building image') {
            steps {
                script {
                    dockerImage = docker.build("${registry}/${imageName}:${env.BUILD_NUMBER}") // Sử dụng cú pháp đúng để build Docker image
                }
            }
        }

        stage('Uploading to Nexus') {
            steps {
                script {
                    docker.withRegistry("http://${registry}", registryCredentials) {
                        dockerImage.push('latest') // Push image với tag 'latest'
                        dockerImage.push("${env.BUILD_NUMBER}") // Push image với tag là số build
                    }
                }
            }
        }

        stage('Stop previous containers') {
            steps {
                script {
                    sh 'docker ps -f name=mywordpress -q | xargs --no-run-if-empty docker container stop'
                    sh 'docker container ls -a -f name=mywordpress -q | xargs -r docker container rm'
                }
            }
        }

        stage('Docker Run') {
            steps {
                script {
                    sh "docker run -d -p 8888:80 --rm --name mywordpress ${registry}/${imageName}:${env.BUILD_NUMBER}" // Chạy container từ image mới nhất
                }
            }
        }
    }
}


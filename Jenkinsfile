pipeline {
    agent any
    stages {
        stage('Pull Image') {
            steps {
                // Pulls the latest image from Docker Hub on the remote server
                sshagent(['docker-server']) {
                    sh 'ssh root@192.168.252.20 "docker pull jinitus/static-website-nginx:latest"'
                }
            }
        }

        stage('Run Container') {
            steps {
                // Stops and removes any existing container, then runs a new one on the remote server
                sshagent(['docker-server']) {
                    sh '''
                        ssh root@192.168.252.20 "docker stop main-container || true"
                        ssh root@192.168.252.20 "docker rm main-container || true"
                        ssh root@192.168.252.20 "docker run --name main-container -d -p 8082:80 jinitus/static-website-nginx:latest"
                    '''
                }
            }
        }

        stage('Test Website') {
            steps {
                // Tests if the website is accessible on the new container on the remote server
                sh 'curl -I http://192.168.252.20:8082 || exit 1'
            }
        }
    }
}

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
                    script {
                        // Generate a dynamic container name using the BUILD_ID or a timestamp
                        def containerName = "main-container-${env.BUILD_ID}"
                        
                        // Stop and remove the existing container (if any)
                        sh '''
                            ssh root@192.168.252.20 "docker stop ${containerName} || true"
                            ssh root@192.168.252.20 "docker rm ${containerName} || true"
                            ssh root@192.168.252.20 "docker run --name ${containerName} -d -p 8082:80 jinitus/static-website-nginx:latest"
                        '''
                    }
                }
            }
        }

        stage('Test Website') {
            steps {
                // Tests if the website is accessible on the new container on the remote server
                script {
                    def response = sh(script: 'curl -I http://192.168.252.20:8082', returnStatus: true)
                    if (response != 0) {
                        error "Website is not accessible. Curl failed."
                    }
                }
            }
        }
    }
}

pipeline {
    agent any
    stages {
        stage('Pull Image') {
            steps {
                sshagent(['docker-server']) {
                    sh 'ssh root@192.168.252.20 "docker pull jinitus/static-website-nginx:latest"'
                }
            }
        }

        stage('Run Container') {
            steps {
                sshagent(['docker-server']) {
                    script {
                        def buildId = env.BUILD_ID ?: "default"
                        def containerName = "main-container-${buildId}"

                        echo "Using container name: ${containerName}"

                        // Stop and remove the existing container on port 8082 (if any)
                        sh """
                            ssh root@192.168.252.20 "docker stop ${containerName} || true"
                            ssh root@192.168.252.20 "docker rm ${containerName} || true"
                            ssh root@192.168.252.20 "docker stop $(docker ps -q -f 'ancestor=jinitus/static-website-nginx:latest' -f 'port=8082') || true"
                            ssh root@192.168.252.20 "docker rm $(docker ps -q -f 'ancestor=jinitus/static-website-nginx:latest' -f 'port=8082') || true"
                            ssh root@192.168.252.20 "docker run --name ${containerName} -d -p 8082:80 jinitus/static-website-nginx:latest"
                        """
                    }
                }
            }
        }

        stage('Test Website') {
            steps {
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

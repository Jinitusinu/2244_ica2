pipeline {
    agent any
    stages {
        stage('Cleanup') {
            steps {
                cleanWs() // Cleans the workspace
            }
        }

        stage('Checkout Code') {
            steps {
                checkout scm // Checks out the code from the repository
            }
        }

        stage('Copy Files to Remote Server') {
            steps {
                sshagent(['docker-server']) {
                    sh '''
                    scp -r Dockerfile Jenkinsfile README.md assets error images index.html root@192.168.252.20:/opt/website_project/
                    '''
                }
            }
        }

        stage('Build Image') {
            steps {
                sshagent(['docker-server']) {
                    sh '''
                    ssh root@192.168.252.20 "cd /opt/website_project && docker build -t static-website-nginx:develop-${BUILD_ID} ."
                    '''
                }
            }
        }

        stage('Run Container') {
            steps {
                sshagent(['docker-server']) {
                    sh '''
                    ssh root@192.168.252.20 "docker stop develop-container || true && docker rm develop-container || true && docker run --name develop-container -d -p 8081:80 static-website-nginx:develop-${BUILD_ID}"
                    '''
                }
            }
        }

        stage('Test Website') {
            steps {
                sshagent(['docker-server']) {
                    sh '''
                    ssh root@192.168.252.20 "curl -I http://192.168.252.20:8081"
                    if ! curl -I http://192.168.252.20:8081; then
                    echo "Website is not accessible, stopping pipeline."
                    exit 1
                    fi
                    '''
                }
            }
        }

        stage('Push Image') {
            steps {
                sshagent(['docker-server']) {
                    withCredentials([usernamePassword(credentialsId: 'dockerhub_auth', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        sh '''
                        ssh root@192.168.252.20 "docker login -u $USERNAME -p $PASSWORD"
                        ssh root@192.168.252.20 "docker tag static-website-nginx:develop-${BUILD_ID} $USERNAME/static-website-nginx:latest"
                        ssh root@192.168.252.20 "docker tag static-website-nginx:develop-${BUILD_ID} $USERNAME/static-website-nginx:develop-${BUILD_ID}"
                        ssh root@192.168.252.20 "docker push $USERNAME/static-website-nginx:latest"
                        ssh root@192.168.252.20 "docker push $USERNAME/static-website-nginx:develop-${BUILD_ID}"
                        '''
                    }
                }
            }
        }
    }
}

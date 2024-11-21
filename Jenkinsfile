
pipeline {
    agent any
    stages {
        stage('Cleanup') {
            steps {
                cleanWs()
            }
        }

        stage('Clone Git Repo') {
            steps {
                checkout scm
            }
        }

        stage('Listing files') {
            steps {
                sh 'ls -l'
            }
        }

        stage('Build and Push') {
            steps {
                echo 'Building..'
                dir('app'){
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-auth', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                        sh '''
                            docker build -t jinitus/2244_ica2:v1 .
                            docker login -u ${USERNAME} -p ${PASSWORD}
                            docker push jinitus/2244_ica2:v1
                        '''
                    }
                }
            }
        }

        stage('Deploy container'){
            steps {
                echo "deploying container"
                sh 'docker stop 2244_ica2 || true && docker rm 2244_ica2 || true'
                sh 'docker run --name 2244_ica2 -d -p 8081:80 jinitus/2244_ica2:v1'
            }
        }

        stage('Test container'){
            steps {
                sh 'curl -I localhost:8081'
            }
        }
    }
}

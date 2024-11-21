pipeline {
    agent any
    environment {
        TAG_DYNAMIC = "${env.GIT_BRANCH.replaceFirst('^origin/','')}-${env.BUILD_ID}"
        }
    stages {
        stage('Cleanup') {
            steps {
                cleanWs()
            }
        }


        stage('Pull image from Dockerhub') {
            steps {
                echo 'Building..'
                    withCredentials([usernamePassword(credentialsId:
'dockerHub_auth', passwordVariable: 'PASSWORD',
usernameVariable: 'USERNAME')]) {
                        sh "docker login -u ${USERNAME} -p ${PASSWORD}"
                        sh "docker pull jinitus/2244_ica2"
                    }
            }
        }

        stage('Create Container and Test'){
            steps {
                sh '''
                    docker stop mywebsite
                    docker rm mywebsite
                    docker run -d -p 8082:80 --name mywebsite
jinitus/2244_ica2
                    curl -I localhost:8082
                    echo "success"
                '''
            }
        }

    }
}

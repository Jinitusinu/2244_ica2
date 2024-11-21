

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

        stage('Clone Git Repo') {
            steps {
                checkout scm
            }
        }


        stage('') {
            steps {
                sh '''
                    docker stop temp_container
                    docker rm temp_container
                    docker build -t jinitus/2244_ica2 .
                    docker run -d -p 8081:80 --name temp_container jinitus/2244_ica2
                    curl -I localhost:8081
                '''
            }
        }

        stage('Build and Push') {
            steps {
                echo 'Building..'
                    withCredentials([usernamePassword(credentialsId:
'dockerHub_auth', passwordVariable: 'PASSWORD',
usernameVariable: 'USERNAME')]) {
                        sh "docker login -u ${USERNAME} -p ${PASSWORD}"
                        sh "docker tag jinitus/2244_ica2 jinitus/2244_ica2:${TAG_DYNAMIC}"
                        sh "docker push jinitus/2244_ica2:${TAG_DYNAMIC}"
                    }
            }
        }

        stage('Image push completed'){
            steps {
                echo 'Completed..'
            }
        }

    }
}


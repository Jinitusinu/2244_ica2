

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

        // stage('Deploy container'){
        //     steps {
        //         echo "deploying container"
        //         sh 'docker stop todo-app || true && docker rm
todo-app || true'
        //         sh 'docker run --name todo-app -d -p 3000:3000
msalim22/todo-list-app:v2'
        //     }
        // }

        // This block is to deploy the container into another
application server.
        // You need sshAgent Jenkins plugin to be installed (from
Managed Jenkins -> Plugin) and SSH communication is enabled between
Jenkins and Application server
        // stage('Deploy container into App server') {
        //     steps {
        //         sshagent(['ssh-key']) {
        //
withCredentials([usernamePassword(credentialsId: 'DockerHubPwd',
usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
        //                 sh '''
        //                     ssh -tt root@<APP_HOST_VM_IP> -o
StrictHostKeyChecking=no "docker pull msalim22/todo-list-app"
        //                     ssh -tt root@<APP_HOST_VM_IP> -o
StrictHostKeyChecking=no "docker stop todolist-app || true && docker
rm todolist-app || true"
        //                     ssh -tt root@<APP_HOST_VM_IP> -o
StrictHostKeyChecking=no "docker run --name todolist-app -d -p
9000:3000 msalim22/todo-list-app"
        //                 '''
        //             }
        //         }
        //     }
        // }
    }
}


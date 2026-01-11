// @Library('Shared')_
// pipeline{
//     agent { label 'dev-server'}
    
//     stages{
//         stage("Code clone"){
//             steps{
//                 sh "whoami"
//             clone("https://github.com/LondheShubham153/django-notes-app.git","main")
//             }
//         }
//         stage("Code Build"){
//             steps{
//             dockerbuild("notes-app","latest")
//             }
//         }
//         stage("Push to DockerHub"){
//             steps{
//                 dockerpush("dockerHubCreds","notes-app","latest")
//             }
//         }
//         stage("Deploy"){
//             steps{
//                 deploy()
//             }
//         }
        
//     }
// }





@Library("shared") _
pipeline {
    agent { label 'Agent-A' }

    stages {

        stage('Code') {
            steps {
                script{
                    clone(repoUrl: "https://github.com/harshalwaghare18/django_notes_app.git",
                          branch: "main")
                    echo 'The app is cloned successfully'
                }
            }
        }

        stage('Build') {
            steps {
                echo 'This is the building stage'
                sh '''#!/bin/bash
                whoami
                docker build -t notes-app:latest .
                '''
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo 'Push the image to the dockerhub'
                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker_hub_cread',
                        usernameVariable: 'dockerHubUser',
                        passwordVariable: 'DockerHubPass'
                    )
                ]) {
                    sh '''#!/bin/bash
                    docker login -u "$dockerHubUser" -p "$DockerHubPass"
                    docker image tag notes-app:latest "$dockerHubUser/notes-app:latest"
                    docker push "$dockerHubUser/notes-app:latest"
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                sh '''#!/bin/bash
                docker rm -f notes-app || true

                docker run -d \
                  --name notes-app \
                  -p 8000:8000 \
                  notes-app:latest \
                  python manage.py runserver 0.0.0.0:8000
                '''
            }
        }
    }
}

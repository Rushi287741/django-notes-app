@Library("shared") _
pipeline{
   agent { label "aws-agent" }
   
    stages{
        
        stage('hello'){
            steps{
                script{
                    hello()
           }
       }
   }
         stage("code"){
             steps{
                script{
                    clone("https://github.com/Rushi287741/django-notes-app.git","main")
                }
             } 
         }
         stage("build"){
             steps{
                 echo "this is build"
                 sh "docker build -t notes-app:latest ."
             }
         }
         stage("pushing to dockerhub"){
             steps{
                 echo "this is testing"
                withCredentials([usernamePassword(
                credentialsId: "dockerHubCred",
                passwordVariable: "dockerHubPass",
                usernameVariable: "dockerHubUser")]){
                sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass} "
                sh "docker image tag notes-app:latest rushi117/notes-app:latest"
                sh "docker push rushi117/notes-app:latest"
                }
             }
         }
         stage("deploy"){
            steps{
                echo "this is deploying"
                sh """
                docker network create django-network || true
                docker stop django_cont || true
                docker rm django_cont || true
                docker stop mysql_cont || true
                docker rm mysql_cont || true
                docker run -d \
                --name mysql_cont \
                --network django-network \
                -e MYSQL_ROOT_PASSWORD=root \
                -e MYSQL_DATABASE=notesdb \
                -e MYSQL_USER=admin \
                -e MYSQL_PASSWORD=admin \
                mysql:5.7
                sleep 20
                docker run -d -p 8000:8000 \
                --name django_cont \
                --network django-network \
                -e DB_HOST=mysql_cont \
                -e DB_NAME=notesdb \
                -e DB_USER=admin \
                -e DB_PASSWORD=admin \
                notes-app:latest
                """
    }
}
     }
}

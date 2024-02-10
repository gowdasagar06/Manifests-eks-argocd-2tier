pipeline {
    agent any
    
    stages{
        stage("Code"){
            steps{
                git url: "https://github.com/gowdasagar06/2-Tier-Flask-App.git", branch: "main"
            }
        }
        stage("Build & Test"){
            steps{
                sh "sudo docker build . -t flaskapp"
            }
        } 
        stage("Push to DockerHub"){
            steps{
                withCredentials([usernamePassword(credentialsId:"dockerHub",passwordVariable:"dockerHubPass",usernameVariable:"dockerHubUser")]){
                    sh "sudo docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "sudo docker tag flaskapp ${env.dockerHubUser}/flaskapp:latest"
                    sh "sudo docker push ${env.dockerHubUser}/flaskapp:latest" 
                }
            }
        }
        stage("Deploy"){
            steps{
                sh "docker run -d --name mysql -v mysql-data:/var/lib/mysql -v ./message.sql:/docker-entrypoint-initdb.d/message.sql -e MYSQL_DATABASE=mydb  -e MYSQL_ROOT_PASSWORD="admin" -p 3306:3306 --network=twotier mysql:5.7"
                sh "docker run -d --name flaskapp -v mysql-data:/var/lib/mysql -v ./message.sql:/docker-entrypoint-initdb.d/message.sql --network=twotier -e MYSQL_HOST=mysql -e MYSQL_USER=root -e MYSQL_PASSWORD=admin -e MYSQL_DB=mydb -p 5000:5000 flaskapp:latest"
            }
        }
    }
}

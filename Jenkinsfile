pipeline{
    agent any
    environment{
        registry = "034362040531.dkr.ecr.us-east-1.amazonaws.com/eyego-repo"
    }

    stages{
        stage("preparation"){
            steps{
                checkout scm
            }
        }


        stage("test"){
            steps{
                
               sh 'sudo yum install npm'
               sh 'npm test'
                
            }
        }
        stage("build"){
            steps{
                
               sh 'npm run build'
                
            }
        }      
        stage("build Image"){
            steps{
                
               sh 'docker build -t eyego-app:latest . '
                
            }
        }    


        stage("push Image to docker hub"){
            steps{
                            withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'password', usernameVariable: 'username')]) {
                            sh """
                                docker login -u ${username} -p ${password}
                                docker tag eyego-app:latest ${username}/eyego-app:latest
                                docker push ${username}/eyego-app:latest
                                docker logout
                                
                                """           
                }   
            }
        }

        
        stage("push image to ECR"){
            steps{
                script{
                sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 034362040531.dkr.ecr.us-east-1.amazonaws.com'
                sh 'docker push 034362040531.dkr.ecr.us-east-1.amazonaws.com/eyego-repo:latest'                 
                }                
            }
        }



    }
}

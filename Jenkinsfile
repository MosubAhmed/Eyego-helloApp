pipeline{
    agent any
    
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
        stage("deploy"){
            steps{
                script{
                    withAWS(credentials: 'aws-cli', region: 'us-east-1') {
                        sh 'aws eks update-kubeconfig --region us-east-1 --name eks'
                        sh 'kubectl apply -f deployment.yaml' 
                }
                }
            }
        }

    }
}

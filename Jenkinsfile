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
    stage('Building image') {
      steps{
        script {
           sh 'docker build -t eyego-app:latest .' 
        }
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
        stage('Push to ECR') {
            steps {
                script {
                    sh '''
                        # Authenticate Docker with AWS ECR
                        aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 034362040531.dkr.ecr.us-east-1.amazonaws.com
        
                        # Build the Docker image
                        docker build -t eyego-repo .
        
                        # Tag the image for ECR
                        docker tag eyego-repo:latest 034362040531.dkr.ecr.us-east-1.amazonaws.com/eyego-repo:latest
        
                        # Push the image to AWS ECR
                        docker push 034362040531.dkr.ecr.us-east-1.amazonaws.com/eyego-repo:latest
                    '''
                }
            }
        }



    }
}

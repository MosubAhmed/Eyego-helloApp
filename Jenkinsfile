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
        stage('Push to ecr') {
            steps {
                script {
                    sh """
                        # login to aws ecr
                        aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 034362040531.dkr.ecr.us-east-1.amazonaws.com
        
                        # build the image
                        docker build -t eyego-repo .
        
                        # Tag the image
                        docker tag eyego-repo:latest 034362040531.dkr.ecr.us-east-1.amazonaws.com/eyego-repo:latest
        
                        # push image to ecr
                        docker push 034362040531.dkr.ecr.us-east-1.amazonaws.com/eyego-repo:latest
                    """
                }
            }
        }

       stage('Deploy to AWS k8s') {
            steps {
                script {
                    withAWS(credentials: 'aws-cli', region: 'us-east-1') {
                        sh """
                        
                        aws eks --region us-east-1 update-kubeconfig --name eyego-eks-cluster
                        kubectl apply -f deployment.yaml
                        
                        """
                }

                }
            }
        }


    }
}

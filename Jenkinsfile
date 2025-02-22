pipeline{
    agent any
 environment {
        AWS_ACCOUNT_ID="034362040531"
        AWS_DEFAULT_REGION="us-east-1"
        IMAGE_REPO_NAME="eyego-repo"
        IMAGE_TAG="latest"
        REPOSITORY_URI = "034362040531.dkr.ecr.us-east-1.amazonaws.com/eyego-repo"
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
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build "${IMAGE_REPO_NAME}:${IMAGE_TAG}"
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

          stage("logging to ECR"){
            steps{
                script{      
                     sh """
                    aws ecr get-login-password --region ${AWS_DEFAULT_REGION} | docker login --username AWS --password-stdin ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com
                    """
                }                
            }
        }

 stage('Pushing to ECR') {
     steps{  
         script {
                sh """docker tag ${IMAGE_REPO_NAME}:${IMAGE_TAG} ${REPOSITORY_URI}:$IMAGE_TAG"""
                sh """docker push ${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_DEFAULT_REGION}.amazonaws.com/${IMAGE_REPO_NAME}:${IMAGE_TAG}"""
         }
        }
      }



    }
}

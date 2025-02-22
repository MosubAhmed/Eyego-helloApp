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
          docker build -t eyego-app:latest .
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


    }
}

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
                
               sh 'yum install npm'
               sh 'npm test'
                
            }
        }
        stage("build"){
            steps{
                
               sh 'npm run build'
                
            }
        }      


    }
}

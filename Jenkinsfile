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
                
               sh 'sudo yum install npm -y'
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

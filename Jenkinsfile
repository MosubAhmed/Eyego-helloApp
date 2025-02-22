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
                
               sh 'docker built -t Eyego-app:latest . '
                
            }
        }    

    }
}

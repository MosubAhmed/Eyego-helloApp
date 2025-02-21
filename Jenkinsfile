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
                sh """
                sudo npm install
                npm test
                """
            }
        }
        stage("build"){
            steps{
                sh """
                npm run build
                """
            }
        }      


    }
}

pipeline {
    agent any

    stages {
        stage("Preparation") {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    extensions: [],
                    userRemoteConfigs: [[
                        credentialsId: 'Github',
                        url: 'https://github.com/MosubAhmed/Eyego-helloApp/'
                    ]]
                ])
            }
        }

        stage("Test") {
            steps {
                sh 'sudo -n apt-get update'
                sh 'sudo -n apt-get install -y npm'
                sh 'npm test'
            }
        }

        stage("Build the app") {
            steps {
                sh 'npm run build'
            }
        }

        stage('Build image') {
            steps {
                script {
                    sh 'docker build -t eyego-app:latest .' 
                }
            }
        }

        stage("Push Image to Docker Hub") {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub', 
                    passwordVariable: 'password', 
                    usernameVariable: 'username'
                )]) {
                    sh '''
                        docker login -u $username -p $password
                        docker tag eyego-app:latest $username/eyego-app:latest
                        docker push $username/eyego-app:latest
                        docker logout
                    '''           
                }   
            }
        }

        stage("Push image to ECR") {
            steps {
                script {
                    sh "aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 034362040531.dkr.ecr.us-east-1.amazonaws.com"
                    sh "docker tag eyego-app:latest 034362040531.dkr.ecr.us-east-1.amazonaws.com/eyego-repo:latest"
                    sh "docker push 034362040531.dkr.ecr.us-east-1.amazonaws.com/eyego-repo:latest"
                }
            }
        }

        stage("Deploy from the dockerhub to K8S") {
            steps {
                script {
                   withAWS(credentials: 'aws-cli', region: 'us-east-1') {
                   sh   'aws eks  update-kubeconfig --name eyego-ekscluster --region us-east-1'
                   sh   'kubectl apply -f nodejs-app.yaml --validate=false'
                       
                            
                            
                    } 
                }
            }
        }




          }
}

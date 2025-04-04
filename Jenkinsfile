pipeline {
    
    agent any 
    
    stages {
        stage('Git Checkout'){
            steps{
                script{
                    git branch: 'newbranch', url: 'https://github.com/GovardhanReddysp/kubernetes-automation-java-project.git'
                    }
                }
            }
        stage('Maven build') {
            
            steps {
                
                script{
                    
                    sh 'mvn clean install'
                }
            }
        }
        stage('test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Docker image  build stage') {
            steps {
                sh 'docker image build -t $JOB_NAME:v.$BUILD_ID .'
            }
        }
        stage('Tag docker image') {
            steps {
                sh 'docker image tag $JOB_NAME:v.$BUILD_ID GovardhanReddysp/$JOB_NAME:v.$BUILD_ID'
                }
        }
       stage ('push docker image to  dockerhub') {
            steps {
                script {
                   withCredentials([string(credentialsId: 'GovardhanReddysp', variable: 'dockerpswd')]) {
                        sh '''
                        docker login -u GovardhanReddysp -p ${dockerpswd}
                        docker image push GovardhanReddysp/$JOB_NAME:v.$BUILD_ID
                        docker rmi $JOB_NAME:v.$BUILD_ID
                        docker rmi GovardhanReddysp/$JOB_NAME:v.$BUILD_ID
                        ''' 
                        }
                    }
                }
            }
        stage('deploy docker image') {
            steps {
                 sh '''
                 docker ps -q -f name=shopping-container && docker stop shopping-container && docker rm shopping-container || echo "Container not found or already stopped."
                 docker run -d -p 9191:8181 --name shopping-container /$JOB_NAME GovardhanReddysp:v.$BUILD_ID
                 '''
            }
        }
    }
}

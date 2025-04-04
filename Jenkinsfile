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
                sh 'docker image tag $JOB_NAME:v.$BUILD_ID govardhanreddysp/$JOB_NAME:v.$BUILD_ID'
                }
        }
       stage ('push docker image to  dockerhub') {
            steps {
                script {
                   withCredentials([string(credentialsId: 'GovardhanReddysp1', variable: 'dockerpswd')]) {
                        sh '''
                        docker login -u govardhanreddysp -p ${dockerpswd}
                        docker image push govardhanreddysp/$JOB_NAME:v.$BUILD_ID
                        docker rmi $JOB_NAME:v.$BUILD_ID
                        docker rmi govardhanreddysp/$JOB_NAME:v.$BUILD_ID
                        ''' 
                        }
                    }
                }
            }
        stage('deploy docker image') {
            steps {
                 sh '''
                 docker ps -q -f name=shopping-container && docker stop shopping-container && docker rm shopping-container || echo "Container not found or already stopped."
                 docker run -d -p 9191:8181 --name shopping-container /$JOB_NAME govardhanreddysp:v.$BUILD_ID
                 '''
            }
        }
    }
}

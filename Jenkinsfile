pipeline {
    agent any 
    stages {
        stage('Git Checkout'){
            steps{
                script{
                    git branch: 'newbranch', url: 'https://github.com/khadar099/kubernetes-automation-java-project.git'
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
                sh 'docker image build -t shopping:v.$BUILD_NUMBER .'
            }
        }
        stage('Tag docker image') {
            steps {
                sh 'docker image tag shopping:v.$BUILD_NUMBER /shopping:v.$BUILD_NUMBER'
                }
        }
       stage ('push docker image to  dockerhub') {
            steps {
                script {
                   withCredentials([string(credentialsId: 'dockerhubpswds', variable: 'dockerhubpswds')]) {
                        sh '''
                        docker login -u govardhanreddysp -p ${dockerhubpswds}
                        docker image push govardhanreddysp/shopping:v.$BUILD_NUMBER
                        docker rmi shopping:v.$BUILD_NUMBER
                        docker rmi govardhanreddysp/shopping:v.$BUILD_NUMBER
                        ''' 
                        }
                    }
                }
            }
        stage('deploy docker image') {
            steps {
                 sh '''
                 docker ps -q -f name=shopping-container && docker stop shopping-container && docker rm shopping-container || echo "Container not found or already stopped."
                 docker run -d -p 9191:8181 --name shopping-container govardhanreddysp/shopping:v.$BUILD_NUMBER
                 '''
            }
        }
    }
}

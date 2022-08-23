pipeline {
    agent {
    label 'slave-1'
    }
 
    stages {
        stage('Get Source Code') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/branch1']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/AmerMustafa/new.git']]])
            }
        }
        stage('remove earlier image') {
            steps {
                sh "docker container stop $JOB_NAME"
                sh "docker rm $JOB_NAME"
                sh "docker rmi -f mdamerm/$JOB_NAME:latest"
            }
        }
        stage('Build Dokerfile') {
            steps {
                sh "docker image build -t $JOB_NAME:v1.$BUILD_ID ."
                sh "docker image tag $JOB_NAME:v1.$BUILD_ID mdamerm/$JOB_NAME:v1.$BUILD_ID"
                sh "docker image tag $JOB_NAME:v1.$BUILD_ID mdamerm/$JOB_NAME:latest"
                sh "docker run -d --name $JOB_NAME -p 8000:80 mdamerm/$JOB_NAME:latest"
            }
        }
        stage("push to dockerhub") {
            steps {
                withCredentials([string(credentialsId: 'dockerhubpassword', variable: 'dockerhubpassword')]) {
                sh "docker login -u mdamerm -p ${dockerhubpassword}"
                sh "docker push mdamerm/$JOB_NAME:v1.$BUILD_ID"
                sh "docker rmi -f $JOB_NAME:v1.$BUILD_ID mdamerm/$JOB_NAME:v1.$BUILD_ID"
                }
            }
        }
    }
}

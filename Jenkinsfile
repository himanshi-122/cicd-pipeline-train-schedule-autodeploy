pipeline {
    agent {
        label 'agent01'
    }
    environment { 
           DOCKER_IMAGE_NAME = "himanshi-122/train-schedule"
           DOCKERHUB_CREDENTIALS = credentials('e4c37abe-ff4f-4438-87eb-afee6a131da2')
    }  
    stages {
        stage('Clone MS-Repo') {
            steps {
                script 
                {

                    checkout([$class: 'GitSCM', branches: [[name: 'master']],  userRemoteConfigs: [[url: 'https://github.com/himanshi-122/cicd-pipeline-train-schedule-autodeploy.git']]])
                  
                 
                }
            }				
        }
        stage('Build') {
            steps { 
                script {
                sh '''
                echo 'Gradle Build Started'
                ./gradlew build --no-daemon
               
               '''
                }   
            }
        }
        stage('Containerize') {
            steps{
                
                sh '''
                  
                 echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
                 docker build -t $DOCKER_IMAGE_NAME cicd-pipeline-train-schedule-autodeploy/Dockerfile .
                 docker push $DOCKER_IMAGE_NAME:latest
                 docker rmi $DOCKER_IMAGE_NAME:latest
                 
                 
                 '''
            }
        }
        stage ('K8S Deploy'){
            steps{
                sh ''' 
                  
                  kubectl get pods
                  kubectl apply -f .cicd-pipeline-train-schedule-autodeploy/train-schedule-kube.yml
                  '''

            } 
        }
    }
}

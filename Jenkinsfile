pipeline {
    agent any
    environment {
        PROJECT_ID = "focused-beacon-353107"
        CLUSTER_NAME = "cluster-web"
        LOCATION = "europe-west4-a"
        CREDENTIALS_ID = 'jenkins-project'
    }
    stages {
        stage('pull from github repo'){
            steps{
                git "https://github.com/Bukunmitanimonure/buksapp-UI.git"
            }
        }
        stage('build docker image'){
            steps{
                sh "docker build -t bukunmi00/buksapp-frontend:${env.BUILD_ID} ."                
            }
        }
        stage('push docker image to dockerhub'){
            steps{
                withCredentials([string(credentialsId: 'DOCKER_PASS', variable: 'docker_pass')]) {
                    sh "docker login -u bukunmi00 -p ${docker_pass}"
                }
                sh "docker push bukunmi00/buksapp-frontend:${env.BUILD_ID}"
            }
            
        }
        stage('deploy on k8 cluster'){
            steps{
                sh "sed -i 's/tagversion/${env.BUILD_ID}/g' frontend-k8.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'frontend-k8.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])   
            }
        }
    }
}

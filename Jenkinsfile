pipeline {
    agent any
    tools{
        maven 'maven'
    }
    
    stages{
        stage('Build Maven'){
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Abhijan2023/devops-automation.git']]])
                sh 'mvn clean install'
            }
        }
        stage('Deploy to K8s'){
            steps{
                script{
                    kubernetesDeploy (configs: 'deploymentservice.yaml',kubeconfigId: 'kubernetes')
                }
            }
        }
        stage('Build docker image'){
            steps{
                script{
                    sh 'docker build -t abhishep006/kubernetes:$BUILD_NUMBER .'
                }
            }
        }
        stage('Push image to hub'){
            steps{
                script{
                    withCredentials([string(credentialsId: 'dockerhubpwd', variable: 'dockerhubpwd')]) {
                    sh 'docker login -u abhishep006 -p ${dockerhubpwd}'
                        
                    }
                    sh 'docker push abhishep006/kubernetes:$BUILD_NUMBER''
                }
            }
        }
         stage('Build Stage'){
            steps{
                sh 'mvn clean install'
            }
         }
        stage('SonarQube Analysis Stage') {
            steps{
                withSonarQubeEnv('sonarqube') { 
                    sh "mvn clean verify sonar:sonar -Dsonar.projectKey=sonar-test"
                }
            }
        }
        stage('Deploy to K8s'){
            steps{
                script{
                    kubernetesDeploy (configs: 'deploymentservice.yaml',kubeconfigId: 'kubernetes')
                }
            }
        }
    
    }    
}

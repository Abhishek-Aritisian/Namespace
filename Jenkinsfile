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
        stage('Build docker image'){
            steps{
                //  sh 'kubectl apply -f pv.yaml'
                script{
                    sh 'docker build -t abhishekp006/kubernetes:$BUILD_NUMBER .'
                }
            }
        }
        stage('Push image to hub'){
            steps{
                script{
                    withCredentials([string(credentialsId: 'dockerhubpwd', variable: 'dockerhubpwd')]) {
                    sh 'docker login -u abhishekp006 -p ${dockerhubpwd}'
                        
                    }
                    sh 'docker push abhishekp006/kubernetes:$BUILD_NUMBER'
                }
            }
        }
         stage('Deploy to K8s'){
            steps{
                 //  sh 'kubectl apply -f pv.yaml'
                //    sh 'kubectl set image deploy/multistage multistage=public.ecr.aws/g5i1d7r1/navya114:1.0.$BUILD_NUMBER'
                script{
                    def namespace = 'dev'
                    def deploymentFile = 'deploymentservice.yaml'
                    
                    sh "kubectl create namespace ${namespace}"
                    sh "kubectl apply -f ${deploymentFile} -n ${namespace}"
                    kubernetesDeploy (configs: 'deploymentservice.yaml',kubeconfigId: 'kubernetes')
                }
            }
        }
    }    
}

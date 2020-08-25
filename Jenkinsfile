pipeline {
     agent any
     stages {
         stage('Build') {
              steps {
                  sh 'echo Building...'
              }
         }
         stage('Lint HTML') {
              steps {
                  sh 'tidy -q -e *.html'
              }
         }
         stage('Build Docker Image') {
              steps {
                  sh 'docker build -t capstone-app .'
              }
         }
         stage('Push Docker Image') {
              steps {
                  withDockerRegistry([url: "", credentialsId: "dockerhub"]) {
                      sh "docker tag Cloud-DevOps-Nanodegree-Udacity-CapstoneProject FairozaAmira/Cloud-DevOps-Nanodegree-Udacity-CapstoneProject"
                      sh 'docker push FairozaAmira/Cloud-DevOps-Nanodegree-Udacity-CapstoneProject'
                  }
              }
         }
         stage('Deploying') {
              steps{
                  echo 'Deploying to AWS...'
                  withAWS(credentials: 'aws', region: 'us-west-2') {
                      sh "eksctl create cluster --name eks-cluster"
                      sh "aws eks --region us-west-2 update-kubeconfig --name eks-cluster --version 1.16 --nodegroup-name standard-workers --node-type t2.medium --nodes 3 --nodes-min 1 --nodes-max 4 --node-ami auto --region us-west-2"
                      sh "kubectl config use-context arn:aws:eks:us-west-2:386907932725:cluster/eks-cluster"
                      sh "kubectl set image deployments/Cloud-DevOps-Nanodegree-Udacity-CapstoneProject Cloud-DevOps-Nanodegree-Udacity-CapstoneProject=FairozaAmira/Cloud-DevOps-Nanodegree-Udacity-CapstoneProject:latest"
                      sh "kubectl apply -f CloudFormation/aws-auth-cm.yaml"
                      sh "kubectl apply -f deployment/deployment.yml"
                      sh "kubectl get nodes"
                      sh "kubectl get deployment"
                      sh "kubectl get pod -o wide"
                      sh "kubectl get service/Cloud-DevOps-Nanodegree-Udacity-CapstoneProject"
                  }
              }
        }
        stage("Cleaning up") {
              steps{
                    echo 'Cleaning up...'
                    sh "docker system prune"
              }
        }
     }
}
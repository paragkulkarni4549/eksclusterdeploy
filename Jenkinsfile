pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-2'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/paragkulkarni4549/eksclusterdeploy.git', branch: 'main'
            }
        }

        stage('Terraform Init') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws-creds'
                ]]) {
                    sh '''
                    terraform init
                    terraform plan
                    '''
                }
            }
        }

        stage('Terraform Apply') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws-creds'
                ]]) {
                    sh '''
                    terraform apply -auto-approve
                    '''
                }
            }
        }

        stage('Configure kubectl') {
            steps {
                sh '''
                aws eks --region $AWS_REGION update-kubeconfig --name eks-ohio-cluster
                kubectl get nodes
                '''
            }
        }
    }
}

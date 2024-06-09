pipeline {
    agent any
    environment {
    AWS_ACCESS_KEY_ID = credentials('AWS_ACCESS_KEY_ID')
    AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
    AWS_DEFAULT_REGION = 'us-east-1'
    }

    stages {
        stage('Checkout repo from Git') {
            steps {
                script {
                    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Glen-Poh/Jenkins-S3-Static-Website-Pipeline.git']])
                }
            }
        }
        stage('Terraform init') {
            steps {
                // If user select 'apply' pipeline will run terraform init. Else it will skip this stage
                script {
                    def initCommand = params.action == 'apply' ? 'terraform init' : 'echo "Skipping terraform init stage because action is not set to apply."'
                    sh initCommand
                }
            }
        }
        stage('Terraform plan') {
            steps {
                // If user select 'apply', terraform apply will run. If user select 'destroy', terraform apply -destroy will run
                script {
                    def terraformCommand = 'terraform plan'
                    if (params.action == 'destroy') {
                        terraformCommand += ' -destroy'
                    }
                    sh terraformCommand
                }
            }
        }
        stage('Terraform apply/destroy') {
            steps {
                input(message: "Please review and approve the Terraform plan.", ok: "Approve")
                script {
                    sh 'terraform $action --auto-approve'
                }
            }
        }
    }
}


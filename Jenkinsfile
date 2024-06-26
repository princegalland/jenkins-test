pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                // Checkout the repository containing Terraform configuration
    
                git branch: 'main', url: 'https://github.com/princegalland/jenkins-test.git'
            }
        }

        stage('Terraform Init') {
            steps {
                // Initialize Terraform
                sh 'terraform init'
            }
        }

        stage('Terraform Validate') {
            steps {
                // Validate the Terraform configuration files
                sh 'terraform validate'
            }
        }

        stage('Terraform Plan') {
            steps {
                // Create an execution plan
                sh 'terraform plan -out=tfplan'
            }
        }

        stage('Terraform Apply') {
            steps {
                // Apply the plan to create infrastructure
                withCredentials([string(credentialsId: 'aws-access-key', variable: 'AWS_ACCESS_KEY'), 
                                 string(credentialsId: 'aws-secret-key', variable: 'AWS_SECRET_KEY')]) {
                    sh '''
                        export TF_VAR_access_key=$AWS_ACCESS_KEY
                        export TF_VAR_secret_key=$AWS_SECRET_KEY
                        terraform apply -auto-approve tfplan
                    '''
                }
            }
        }
    }

    post {
        always {
            // Clean up the workspace after the job
            cleanWs()
            echo 'Pipeline completed.'
        }

        success {
            echo 'Terraform apply succeeded.'
        }

        failure {
            echo 'Terraform apply failed.'
        }
    }
}


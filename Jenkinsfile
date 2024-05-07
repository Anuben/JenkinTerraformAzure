pipeline {
    agent any
    environment {
       MY_CRED = credentials('azurelogin')
    } 
    stages {
        stage('Git checkout') {
            steps {
                git 'https://github.com/S-I-N-D-H-U-J-A/JenkinTerraformAzure'
            }
        }
        stage('azurelogin') {
            steps {
                bat 'az login --service-principal -u %MY_CRED_CLIENT_ID% -p %MY_CRED_CLIENT_SECRET% -t %MY_CRED_TENANT_ID%'
            }
        }
        stage('Terraform Init') {
            steps {
                bat 'terraform init'
            }
        }
        stage('Terraform Validate') {
            steps {
                bat 'terraform validate'
            }
        }
        stage('Terraform Plan') {
            steps {
                withCredentials([[
                    $class: 'AzureServicePrincipal',
                    credentialsId: 'azurelogin',
                    subscriptionIdVariable: 'ARM_SUBSCRIPTION_ID',
                    clientIdVariable: 'ARM_CLIENT_ID',
                    clientSecretVariable: 'ARM_CLIENT_SECRET',
                    tenantIdVariable: 'ARM_TENANT_ID'
                ]]) {
                    bat 'terraform plan'
                }
            }
        }
        stage('Terraform Apply') {
            steps {
                withCredentials([[
                    $class: 'AzureServicePrincipal',
                    credentialsId: 'azurelogin',
                    subscriptionIdVariable: 'ARM_SUBSCRIPTION_ID',
                    clientIdVariable: 'ARM_CLIENT_ID',
                    clientSecretVariable: 'ARM_CLIENT_SECRET',
                    tenantIdVariable: 'ARM_TENANT_ID'
                ]]) {
                    bat 'terraform apply -lock=false -auto-approve'
                }
            }
        }
    }
    post {
        failure {
            echo "Jenkins Build Failed"
        }
        success {
            echo "Jenkins Build Success"
        }
        always {
            cleanWs()
        }
    }
}

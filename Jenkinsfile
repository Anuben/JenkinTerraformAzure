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
                powershell 'az login --service-principal -u $env:MY_CRED_CLIENT_ID -p $env:MY_CRED_CLIENT_SECRET -t $env:MY_CRED_TENANT_ID'
            }
        }
        stage('Terraform Init'){
            steps{
                powershell """
                    Write-Host "Initialising Terraform"
                    terraform init
                """
            }
        }
        stage('Terraform Validate'){
            steps {
                powershell """
                    Write-Host "validating Terraform Code"
                    terraform validate
                """
            }
        }
        stage('Terraform Plan'){
            steps {
                withCredentials([azureServicePrincipal(
                    credentialsId: 'azurelogin',
                    subscriptionIdVariable: 'ARM_SUBSCRIPTION_ID',
                    clientIdVariable: 'ARM_CLIENT_ID',
                    clientSecretVariable: 'ARM_CLIENT_SECRET',
                    tenantIdVariable: 'ARM_TENANT_ID'
                )]) {
                    powershell """
                        Write-Host "Plan Terraform"
                        terraform plan
                    """
                }
            }
        }
        stage('Terraform apply') {
            steps {
                withCredentials([azureServicePrincipal(
                    credentialsId: 'azurelogin',
                    subscriptionIdVariable: 'ARM_SUBSCRIPTION_ID',
                    clientIdVariable: 'ARM_CLIENT_ID',
                    clientSecretVariable: 'ARM_CLIENT_SECRET',
                    tenantIdVariable: 'ARM_TENANT_ID')]){
                        powershell """
                            Write-Host "Apply Terraform"
                            terraform apply -lock=false -auto-approve
                        """
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
        

pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Snyk IaC Scan Monitor') {
            steps {
                // No credentials needed here if configured globally
                snykSecurity(
                    failOnIssues: false,
                    severity: 'high',
                    monitorFor: 'iac',
                    monitorBuildOnSuccess: true,
                    additionalArguments: '--json --report'
                )
            }
        }
        
        stage('Terraform Init') {
            steps {
                sh 'terraform init'
            }
        }
        
        stage('Terraform Plan') {
            steps {
                sh 'terraform plan'
            }
        }
        
        stage('Optional Destroy') {
            when {
                expression { params.DESTROY == true }
            }
            steps {
                sh 'terraform destroy -auto-approve'
            }
        }
    }
}
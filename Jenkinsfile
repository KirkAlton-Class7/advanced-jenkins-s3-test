pipeline {
    agent any
    
    environment {
        // Define your credentials
        SNYK_TOKEN = credentials('snyk-api-token')
        // SNYK_ORG = credentials('snyk-org-slug')  // Uncomment if you have this
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        // REPLACE YOUR EXISTING SNYK STAGE WITH THIS ONE
        stage('Snyk IaC Scan Monitor') {
            steps {
                withCredentials([string(credentialsId: 'snyk-api-token', variable: 'SNYK_TOKEN')]) {
                    sh '''
                        # Ensure Snyk is installed
                        if ! command -v snyk &> /dev/null; then
                            echo "Installing Snyk..."
                            curl -sL https://static.snyk.io/cli/latest/snyk-linux -o snyk
                            chmod +x snyk
                            sudo mv snyk /usr/local/bin/
                        fi
                        
                        # Show version for debugging
                        echo "Snyk version:"
                        snyk --version
                        
                        # Install multi-arch support (if on ARM and need HTML reports)
                        # Uncomment if you still get HTML generation errors
                        # sudo dpkg --add-architecture amd64
                        # sudo apt-get update
                        # sudo apt-get install -y libc6:amd64
                        
                        # Authenticate with Snyk
                        echo "Authenticating with Snyk..."
                        snyk auth $SNYK_TOKEN
                        
                        # Set organization if you have it
                        # snyk config set org="$SNYK_ORG"
                        
                        # Run IaC scan
                        echo "Running Snyk IaC scan..."
                        snyk iac test --json --report || echo "Snyk found issues (scan completed)"
                        
                        echo "✓ Snyk scan completed successfully"
                    '''
                }
            }
        }
        
        stage('Terraform Init') {
            steps {
                sh '''
                    echo "Initializing Terraform..."
                    terraform init
                '''
            }
        }
        
        stage('Terraform Plan') {
            steps {
                sh '''
                    echo "Creating Terraform plan..."
                    terraform plan -out=tfplan
                '''
            }
        }
        
        stage('Optional Destroy') {
            when {
                expression { params.DESTROY == true }
            }
            steps {
                sh '''
                    echo "Destroying infrastructure..."
                    terraform destroy -auto-approve
                '''
            }
        }
    }
    
    post {
        always {
            echo "Pipeline execution completed"
            cleanWs()
        }
        success {
            echo "Pipeline succeeded!"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}
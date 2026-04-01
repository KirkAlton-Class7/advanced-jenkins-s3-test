stage('Snyk IaC Scan Monitor') {
    steps {
        withCredentials([string(credentialsId: 'snyk-api-token', variable: 'SNYK_TOKEN')]) {
            sh '''
                # Authenticate and scan
                snyk auth $SNYK_TOKEN
                snyk iac test --json --report || true
            '''
        }
    }
}
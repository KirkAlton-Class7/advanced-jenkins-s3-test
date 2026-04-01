# New Jenkins server test with terraform deployment and triggers

## Jenkinsfile

A simple declarative Jenkinsfile
- Clones git repo 
- Binds AWS IAM user creds in terraform stages with AWS Creds plugin
- Stages for terraform init and apply 
- Destroy stage using user input 

## Terraform script 
- A simple AWS S3 bucket is deployed
- State file is stored in S3 backend 
- S3 bucket name uniqueness is guranteed 

## User data
EC2 startup script to bootstrap Jenkins server

## Tests
Test 1

## NOTES
Must use on an EC2. You'll experience errors with M series Mac machines.
If Jenkins is running on ARM64 architecture (Apple Silicon/Mac) and the snyk-to-html-linux binary is an x86/amd64 binary, it can't run on ARM without multi-arch libraries.
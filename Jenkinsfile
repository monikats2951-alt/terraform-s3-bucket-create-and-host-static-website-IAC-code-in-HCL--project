```groovy
pipeline {
    agent any

    stages {

        stage('Pull Code from GitHub') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/rajeshark/terraform-s3-bucket-create-and-host-static-website-IAC-code-in-HCL--project.git'
            }
        }

        stage('Terraform Init & Apply') {
            steps {
                withAWS(credentials: 'aws-cred-rajesh', region: 'ap-south-1') {
                    sh 'terraform init'
                    sh 'terraform validate'
                    sh 'terraform apply -auto-approve'
                }
            }
        }

        stage('Upload Files to S3') {
            steps {
                withAWS(credentials: 'aws-cred-rajesh', region: 'us-east-1') {
                    sh '''
                        BUCKET_NAME=$(terraform output -raw name)

                        echo "Uploading files to S3 bucket: $BUCKET_NAME"

                        aws s3 sync ./ s3://$BUCKET_NAME \
                            --exclude ".git/*" \
                            --exclude ".terraform/*" \
                            --exclude "terraform.lock.hcl" \
                            --exclude "*.tf" \
                            --exclude "*.hcl" \
                            --exclude "Jenkinsfile" \
                            --exclude "*.md"
                    '''
                }
            }
        }
    }

    post {
        success {
            echo 'Static website deployment successful!'
            sh 'terraform output -raw name'
        }

        failure {
            echo 'Static website deployment failed!'
        }
    }
}
```

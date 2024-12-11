pipeline {
    agent any

    environment {
        TF_WORKSPACE = 'default'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/mukesh3/k8s-gke.git', branch: 'main'
            }
        }

        stage('Setup Google Cloud SDK') {
            steps {
                script {
                    // Use the secret file credential
                    withCredentials([file(credentialsId: 'glcoud_svc-tf-svc', variable: 'GOOGLE_APPLICATION_CREDENTIALS')]) {
                        // Install Google Cloud SDK if not already installed
                        def gcloudInstalled = sh(script: 'gcloud version', returnStatus: true)
                        if (gcloudInstalled != 0) {
                            sh 'curl https://sdk.cloud.google.com | bash'
                            sh 'exec -l $SHELL'
                            sh 'gcloud init'
                        }
                        
                        // Authenticate with Google Cloud
                        sh 'gcloud auth activate-service-account --key-file=$GOOGLE_APPLICATION_CREDENTIALS'
                        // Set the project (replace 'your-project-id' with your actual project ID)
                        sh 'gcloud config set project your-project-id'
                    }
                }
            }
        }

        stage('Terraform Init') {
            steps {
                sh 'terraform init'
            }
        }

        stage('Terraform Validate') {
            steps {
                sh 'terraform validate'
            }
        }

        stage('Terraform Plan') {
            steps {
                sh 'terraform plan -out=terraform.plan'
            }
        }

        stage('Terraform Apply') {
            steps {
                input message: 'Approve Terraform Apply?', ok: 'Apply'
                sh 'terraform apply terraform.plan'
            }
        }

        stage('Clean Up') {
            steps {
                sh 'rm -f terraform.plan'
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '**/terraform.plan', allowEmptyArchive: true
            cleanWs()
        }
        success {
            echo 'Terraform applied successfully!'
        }
        failure {
            echo 'Terraform failed to apply.'
        }
    }
}

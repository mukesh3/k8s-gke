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
                        sh 'gcloud config set project skilled-drake-444315-g3'
                    }
                }
            }
        }

        stage('Terraform Init') {
            steps {
                dir('gke-tf'){
                sh 'terraform init'
            }}
        }

        stage('Terraform Validate') {
            steps {
                dir('gke-tf'){
                sh 'terraform validate'
            }}
        }

        stage('Terraform Plan') {
            steps {
                dir('gke-tf'){
                sh 'terraform plan -out=terraform.plan'
            }}
        }

        stage('Terraform Apply') {
            steps {
                dir('gke-tf'){
                input message: 'Approve Terraform Apply?', ok: 'Apply'
                sh 'terraform apply terraform.plan'
            }}
        }

        stage('Clean Up') {
            steps {
                dir('gke-tf'){
                sh 'rm -f terraform.plan'
            }}
        }
    }
}

pipeline {
    agent any

    environment {
        TF_WORKSPACE = 'default'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://your-repo-url.git', branch: 'main'
            }
        }

        #stage('Install Terraform') {
        #    steps {
        #        script {
        #            def terraformInstalled = sh(script: 'terraform --version', returnStatus: true)
        #            if (terraformInstalled != 0) {
        #                sh 'curl -sLo /tmp/terraform.zip https://releases.hashicorp.com/terraform/1.0.0/terraform_1.0.0_linux_amd64.zip'
        #                sh 'unzip /tmp/terraform.zip -d /usr/local/bin/'
        #            }
        #        }
        #    }
        #}

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

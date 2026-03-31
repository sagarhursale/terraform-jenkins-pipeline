pipeline {
    agent any

    parameters {
        booleanParam(name: 'autoApprove', defaultValue: false, description: 'Automatically run apply after generating plan?')
        choice(name: 'action', choices: ['apply', 'destroy'], description: 'Select the action to perform')
    }

    environment {
        AWS_ACCESS_KEY_ID     = credentials('aws-access-key-id')
        AWS_SECRET_ACCESS_KEY = credentials('aws-secret-access-key')
        AWS_DEFAULT_REGION    = 'ap-south-1'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Devops1224789/Devops-Mega-project.git'
            }
        }

        // ✅ Debug (keep for now, remove later)
        stage('Debug') {
            steps {
                sh 'pwd'
                sh 'ls -la'
                sh 'find . -name "*.tf"'
            }
        }

        stage('Terraform Init') {
            steps {
                sh 'terraform init'
            }
        }

        stage('Plan') {
            steps {
                sh 'terraform plan -out=tfplan'
                sh 'terraform show -no-color tfplan > tfplan.txt'
            }
        }

        stage('Apply / Destroy') {
            steps {
                script {

                    if (params.action == 'apply') {

                        if (!params.autoApprove) {
                            def plan = readFile 'tfplan.txt'
                            input message: "Do you want to apply the plan?",
                            parameters: [text(name: 'Plan', description: 'Review Terraform Plan', defaultValue: plan)]
                        }

                        sh "terraform apply -input=false tfplan"

                    } else if (params.action == 'destroy') {

                        sh "terraform destroy --auto-approve"

                    } else {
                        error "Invalid action selected."
                    }
                }
            }
        }
    }
}

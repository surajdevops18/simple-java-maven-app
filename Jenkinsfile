pipeline {

    agent {
        node {
            label 'master_node'
        }
    }

    environment {
        GIT_CREDENTIALS = 'github-ssh-key' // ID from Jenkins credentials
       
    }

    

    stages {
        
        stage('Cleanup Workspace') {
            steps {
                cleanWs()
                sh """
                echo "Cleaned Up Workspace For Project"
                """
            }
        }

        stage('Code Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM', 
                    git branch: 'master',
                    url: 'git@github.com:surajdevops18/simple-java-maven-app.git',
                    credentialsId: "${env.GIT_CREDENTIALS}"
                ])
            }
        }

        stage('Unit Testing') {
            steps {
                sh """
                echo "Running Unit Tests"
                """
            }
        }

        stage('Code Analysis') {
            steps {
                sh """
                echo "Running Code Analysis"
                """
            }
        }

        stage('Deploy To Dev & QA') {
            when {
                branch 'develop'
            }
            steps {
                sh """
                echo "Building Artifact for Dev Environment"
                """
                sh """
                echo "Deploying to Dev Environment"
                """
                sh """
                echo "Deploying to QA Environment"
                """
            }
        }

        stage('Deploy To Staging and Pre-Prod Code') {
            when {
                branch 'main'
            }
            steps {
                sh """
                echo "Building Artifact for Staging and Pre-Prod Environments"
                """
                sh """
                echo "Deploying to Staging Environment"
                """
                sh """
                echo "Deploying to Pre-Prod Environment"
                """
            }
        }

    }   
}
        

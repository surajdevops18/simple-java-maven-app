pipeline {
    agent any

    environment {
        GIT_CREDENTIALS = 'github-ssh-key' // ID from Jenkins credentials
       
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'master',
                    url: 'git@github.com:surajdevops18/simple-java-maven-app.git',
                    credentialsId: "${env.GIT_CREDENTIALS}"
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        
        stage('Deploy to Tomcat') {
            steps {
                sshagent(credentials: ['tomcat-ssh']){
                    //scp -o strictHostKeyChecking=no target/maven-web-app.war ec2-user@172.31.44.111:/usr/local/tomcat/webapps/
                    sh '''
                    
                    scp -o StrictHostKeyChecking=no target/*.war ec2-user@172.31.17.215:/tmp/
                    ssh -o StrictHostKeyChecking=no ec2-user@172.31.17.215 'sudo mv /tmp/*.war /usr/local/tomcat/webapps/'
					ssh -o StrictHostKeyChecking=no ec2-user@172.31.17.215 'sudo /usr/local/tomcat/bin/startup.sh'
                    '''
                }
            }
        }
    }

    post {
        always {
            junit 'target/surefire-reports/*.xml'
        }
    }
}

pipeline {
  agent any
  options { skipDefaultCheckout true } // we control checkout inside sshagent
  environment {
    GIT_CREDENTIALS     = 'github-ssh-key'     // SSH key credential for GitHub
    TOMCAT_DEV_CRED     = 'tomcat-ssh-dev'     // Jenkins credential id for dev host
    TOMCAT_PROD_CRED    = 'tomcat-ssh-prod'    // Jenkins credential id for prod host
    DEV_HOST            = '172.31.17.215'          // replace with your dev host IP/DNS
    PROD_HOST           = '172.31.32.158'      // replace with your prod host IP/DNS
  }

  stages {
    stage('Checkout') {
      steps {
        // Inject SSH key for Git checkout (works in multibranch pipeline)
        sshagent (credentials: [env.GIT_CREDENTIALS]) {
          checkout scm
        }
      }
    }

    stage('Build') {
      steps {
        script {
          if (isUnix()) {
            sh 'mvn -B -DskipTests package'
          } else {
            bat 'mvn -B -DskipTests package'
          }
        }
      }
    }

    stage('Test') {
      when { branch 'dev' } // run tests only on dev branch (example)
      steps {
        script {
          if (isUnix()) { sh 'mvn test' } else { bat 'mvn test' }
        }
      }
    }

    stage('Deploy') {
      when { anyOf { branch 'dev'; branch 'main' } }
      steps {
        script {
          if (env.BRANCH_NAME == 'main') {
            // Deploy to production
            sshagent (credentials: [env.TOMCAT_PROD_CRED]) {
              sh """
                scp -o StrictHostKeyChecking=yes target/*.war ec2-user@${env.PROD_HOST}:/tmp/
                ssh -o StrictHostKeyChecking=yes ec2-user@${env.PROD_HOST} 'sudo mv /tmp/*.war /usr/local/tomcat/webapps/ && sudo $CATALINA_HOME/bin/startup.sh'
              """
            }
          } else {
            // Deploy to dev
            sshagent (credentials: [env.TOMCAT_DEV_CRED]) {
              sh """
                scp -o StrictHostKeyChecking=yes target/*.war ec2-user@${env.DEV_HOST}:/tmp/
                ssh -o StrictHostKeyChecking=yes ec2-user@${env.DEV_HOST} 'sudo mv /tmp/*.war /usr/local/tomcat/webapps/ && sudo $CATALINA_HOME/bin/startup.sh'
              """
            }
          }
        }
      }
    }
  }

  post {
    always {
      junit 'target/surefire-reports/*.xml'
      archiveArtifacts artifacts: 'target/*.war', fingerprint: true
    }
  }
}

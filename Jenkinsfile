pipeline {
  agent any 
  tools {
    maven 'Maven'
  }
  stages {
    stage ('Initialize') {
      steps {
        sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
            ''' 
      }
    }
    stage ('git-secrets') {
      steps {
        sh 'rm trufflehog || true'
        sh 'docker run gesellix/trufflehog --json https://github.com/roshanrojamar/webapp.git > trufflehog'  
        sh 'cat trufflehog'
    }
    }
    stage ('Build') {
      steps {
        sh 'mvn clean package'
      }
      }
    stage ('Deploy-To-Tomcat') {
            steps {
           sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war ec2-user@ec2-13-232-35-220.ap-south-1.compute.amazonaws.com:/home/ec2-user/prod/apache-tomcat-8.5.55/webapps/webapp.war'
              }      
           }       
    }
  }
}

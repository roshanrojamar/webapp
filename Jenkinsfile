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
    stage ('Check-Git-Secrets') {
      steps {
        sh 'rm trufflehogs || true'
        sh 'sudo docker run gesellix/trufflehog --json https://github.com/cehkunal/webapp.git > trufflehogs'
        sh 'cat trufflehogs'
      }
    }
    stage ('Source Composition Analysis') {
      steps {
         sh 'rm owasp* || true'
         sh 'wget "https://raw.githubusercontent.com/cehkunal/webapp/master/owasp-dependency-check.sh" '
         sh 'chmod +x owasp-dependency-check.sh'
         sh 'sudo bash owasp-dependency-check.sh'
         sh 'cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
         
        
      }
    }
    stage ('Build') {
      steps {
        sh 'mvn clean package'
      }
      }
    stage ('Deploy-To-Tomcats') {
            steps {
           sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war ec2-user@ec2-13-232-35-220.ap-south-1.compute.amazonaws.com:/home/ec2-user/prod/apache-tomcat-8.5.55/webapps/webapp.war'
              }      
           }       
    }
  }
}

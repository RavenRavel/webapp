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
        sh 'rm trufflehog || true'
        sh 'docker run gesellix/trufflehog --json https://github.com/cehkunal/webapp.git > trufflehog'
        sh 'cat trufflehog'
      }
    }
    
    stage ('Deploy-To-Tomcat') {
            steps {
           sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@13.36.209.84:/prod/apache-tomcat-8.5.96/webapps/webapp.war'
              }      
           }       
    }
    
        
    stage ('Build') {
      steps {
      sh 'mvn clean package'
       }
    }
     stage ('DAST') {
      steps {
        sshagent(['zap']) {
         sh 'ssh -o  StrictHostKeyChecking=no ubuntu@13.39.17.154 "docker run -t owasp/zap2docker-stable zap-baseline.py -t http://13.36.209.84:8080/webapp/" || true'
        }
      }
    }
  }
}

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
    
    stage ('Deploy-To-Tomcat') {
            steps {
           sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@13.36.209.84:/prod/apache-tomcat-8.5.39/webapps/webapp.war'
              }      
           }       
    }
    
        
    stage ('Build') {
      steps {
      sh 'mvn clean package'
       }
    }
  }
}

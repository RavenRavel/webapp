pipeline {
  agent any
  tools {
    maven 'Maven'
  }
    stages {
      stage ('Initialize'){
        steps {
          sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
            '''   
        }
      }
     stage ('Check-Git-Secret'){
       steps {
         sh 'rm trufflehog || true'
         sh 'docker run gesellix/trufflehog https://github.com/RavenRavel/webapp.git > trufflehog'
         sh 'cat trufflehog'
       }
     }
      stage ('Source Composition Analysis') {
      steps {
         sh 'rm owasp* || true'
         sh 'wget "https://raw.githubusercontent.com/cehkunal/webapp/master/owasp-dependency-check.sh" '
         sh 'chmod +x owasp-dependency-check.sh'
         sh 'bash owasp-dependency-check.sh'
         sh 'cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
        
      }
    }
     stage ('Build'){
       steps {
       sh 'mvn clean package'
       }
    }
     stage ('Deploy-To-Tomcat') {
      steps {
        sshagent(['tomcat']) {
          sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@13.39.87.158:/prod/apache-tomcat-8.5.96/webapps/webapp.war'
        }   
      }
    }
  }
}

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
        sh ' docker run gesellix/trufflehog --json https://github.com/Adityajaa/finalproject.git > trufflehog'
        sh 'cat trufflehog'
        
      }
    }
    
    stage ('Source Composition Analysis') {
      steps {
         sh 'rm owasp* || true'
         sh 'wget "https://raw.githubusercontent.com/Adityajaa/finalproject/master/owasp-dependency-check.sh#" '
         sh 'chmod +x owasp-dependency-check.sh'
         sh 'bash owasp-dependency-check.sh' 
         
      }
    }
    
    stage ('Build') {
      steps {
      sh 'mvn clean package'
     }       
   } 
    
    stage ('Deploy-To-Tomcat') {
            steps {
           sshagent(['192.168.80.225']) {
                sh 'sshpass -p Adi123 scp -o StrictHostKeyChecking=no /var/lib/jenkins/workspace/webapp-cicd-pipeline/target/*.war shuhari@192.168.80.225:/home/shuhari/prod/apache-tomcat-9.0.64/webapps/webapp.war'
              }      
           }       
    }
  
  
  }
}


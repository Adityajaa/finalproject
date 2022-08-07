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
    
     stage ('SAST') {
      steps {
        withSonarQubeEnv('sonar') {
          sh 'mvn sonar:sonar -Dsonar.login=782fd88b051dcfe48f14052814559ee4bd333f18'
          sh 'cat target/sonar/report-task.txt'
        }
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
     
     stage ('DAST') {
      steps {
        sshagent(['192.168.80.227']) {
         sh 'sshpass -p Adi123 ssh -o  StrictHostKeyChecking=no root@192.168.80.227 "docker run -t owasp/zap2docker-stable zap-baseline.py -t http://192.168.80.225:8080/webapp/" || true '
        }
      }
    }
  
  }
}


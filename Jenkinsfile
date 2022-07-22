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
        sh 'docker run gesellix/trufflehog --json https://github.com/Adityajaa/finalproject.git > trufflehog'
        sh 'cat trufflehog'
      }
    }
    stage ('Source Composition Analysis') {
      steps {
         sh 'rm owasp* || true'
         sh 'wget "https://raw.githubusercontent.com/Adityajaa/finalproject/main/owasp-dependency.sh" '
         sh 'chmod +x owasp-dependency.sh'
         sh 'bash owasp-dependency.sh'
         
        
      }
    }
    
     stage ('SAST') {
      steps {
        withSonarQubeEnv('sonar') {
          sh 'mvn sonar:sonar -Dsonar.login=e7239fbbf917443f17c649c678c76d7a1e57f713'
          sh 'cat target/sonar/report-task.txt '
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
                sh 'scp -o StrictHostKeyChecking=no target/*.war ashish@192.168.80.225:/home/shuhari/prod/apache-tomcat-9.0.64/webapps/webapp.war'
              }      
           }       
    }
    stage ('DAST') {
      steps {
        sshagent(['192.168.80.225']) {
         sh 'ssh -o  StrictHostKeyChecking=no shuhari@192.168.80.225 "docker run -t owasp/zap2docker-stable zap-baseline.py -t http://192.168.80.225:8080/webapp/" || true '
        }
      }
    }
}
}

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

     stage ('Build') {
      steps {
      sh 'mvn clean package'
       }
    }
    stage ('Deploy-To-Tomcat') {
            steps {
           sshagent(['192.168.80.131']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war ashish@192.168.80.131:/home/ashish/prod/apache-tomcat-9.0.60/webapps/webapp.war'
              }      
           }       
    }

}
}

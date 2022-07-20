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
           sshagent(['192.168.80.225']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war shuhari@192.168.80.225:/home/shuhari/prod/apache-tomcat-9.0.64/webapps/webapp.war'
              }      
           }       
    }

}
}

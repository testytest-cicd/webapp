pipeline {
    agent any 
    tools {
        maven 'Maven'
    }
    stages {
        stage ('Initialize') {
            steps {
                sh  '''

                        echo "PATH = ${PATH}"
                        echo "M2_HOME = ${M2_HOME}"

                    ''' 
            }
        }
        stage ('Check-Git-Secrets') {
            steps {
                sh 'rm trufflehog || true'
                sh 'docker run gesellix/trufflehog --json https://github.com/testytest-cicd/webapp.git > trufflehog'
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
                sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@3.70.77.206:/opt/tomcat/webapps/webapp.war'
              }      
           }       
        }
    }
}

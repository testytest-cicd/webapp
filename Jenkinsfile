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
		stage('Error') {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    sh "exit 1"
                }
            }
        }
        stage ('Source Composition Analysis') {
            steps {
                sh 'rm owasp* || true'
                sh 'wget "https://raw.githubusercontent.com/testytest-cicd/webapp/master/owasp-dependency-check.sh" '
                sh 'chmod +x owasp-dependency-check.sh'
                sh 'bash owasp-dependency-check.sh'
                sh 'cat /var/lib/jenkins/OWASP-Dependency-Check/reports/dependency-check-report.xml'
        
      }
    }
		stage('Error') {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    sh "exit 1"
                }
            }
        }
        stage ('SAST') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh 'mvn sonar:sonar'
                    sh 'cat target/sonar/report-task.txt'
        }
      }
    }
		stage('Error') {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    sh "exit 1"
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
           sshagent(['tomcat']) {
                sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@3.70.77.206:/opt/tomcat/webapps/webapp.war'
              }      
           }       
        }
    }
}

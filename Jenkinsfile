pipeline {
    agent any

    stages {
        stage('Initialize') {
            steps {
                sh '''
                
                    echo 'Hello World'
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                    
                '''
            }
        }
        
        stage('Build') {
            steps {
              sh 'mvn clean package'
            
            }
        }
        
        stage('Deploy to tomcat'){
            steps {
                sshagent(['tomcat-ssh']) {
                    sh 'scp -v -o StrictHostKeyChecking=no target/*.war tomcat-dev@10.10.0.4:~/apache-tomcat-8.5.57/webapps/webapp.war'
                }
            }
        }
        
    }
}

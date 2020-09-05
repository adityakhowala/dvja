pipeline {
    agent any

    stages {
        stage('Initialize') {
            steps {
                sh '''
                    echo "==============================="
                    echo "Initializing DEVSECOPS pipeline"
                    echo "==============================="
                '''
            }
        }
        
        stage('Post Commit Check Secrets') {
            steps {
                sh '''
                    rm truffle_report.json || :
                    docker run -t dxa4481/trufflehog https://github.com/adityakhowala/dvja || :
                    #docker run -t dxa4481/trufflehog https://github.com/adityakhowala/dvja > truffle_report.json || :
                    #cat truffle_report.json
                    echo ""
                    echo "======================================================================"
                '''
            }
        }
        
        stage('Software composition analysis') {
            steps {
                sh '''
                    mkdir odc-reports || :
                    chmod +x OWASP-Dependency-Check.sh
                    sh OWASP-Dependency-Check.sh
                    echo ""
                    echo "================================================================================"
                '''
            }
        }
                
        stage('Build') {
            steps {
              sh 'mvn clean package'
            
            }
        }
        //stage('SAST scan') {
        //    steps {
        //            sh 'mvn clean sonar:sonar'
        //            sh 'target/sonar/report-task.txt'
        //        }
        //    }
        //}
        stage('Deploy to tomcat'){
            steps {
                sshagent(['tomcat-ssh']) {
                    sh 'scp -o StrictHostKeyChecking=no target/*.war tomcat-dev@10.10.0.4:~/apache-tomcat-8.5.57/webapps/webapp.war'
                }
            }
        }
        
        stage('DAST Scan'){
            steps {
                sshagent(['dast-machine-zap']) {
                    sh 'ssh -o StrictHostKeyChecking=no dast-machine@10.10.0.6 "docker run -t owasp/zap2docker-stable zap-baseline.py -t http://104.209.147.202:8080/webapp" || :'
                }
            }
        }
        
    }
}

pipeline {
    agent any
    stages {
        stage('Building war') {
            steps {
                sh 'mvn clean package'
                sh 'mv target/*.war target/myweb.war'
            }
        }
        stage('pushing war to S3') {
            steps {
              sh'''
                   sudo yum update -y
                   sudo yum install awscli -y
                   sudo aws configure set aws_access_key_id BKIAUSV5QX5HK6FOD37H
                   sudo aws configure set aws_secret_access_key um8WhOugoaOrpuDtQZFRED0265IMlvZL8HO9InbM
                   sudo aws configure set region us-east-1
                   aws s3 ls
                   sudo mv /var/lib/jenkins/workspace/tomcat/target/myweb.war /var/lib/jenkins/workspace/tomcat/target/myweb-${BUILD_ID}.war
                '''
                   sh 'sudo aws s3 cp /var/lib/jenkins/workspace/tomcat/target/myweb-${BUILD_ID}.war s3://mybucket9731'
                
                }
            }
        
        stage('s3-to-tomcat') {
            steps {
                  sshagent(credentials: ['ec2']) {
            sh '''
                  ssh  -o StrictHostKeyChecking=no  ec2-user@3.82.232.41<<EOF
                  sudo yum update -y
                  sudo yum install awscli -y  
                  sudo aws configure set aws_access_key_id BKIAUSV5QX5HK6FOD37H
                  sudo aws configure set aws_secret_access_key um8WhOugoaOrpuDtQZFRED0265IMlvZL8HO9InbM
                  sudo aws configure set region us-east-1
                  aws s3 ls
                  aws s3 cp s3://mybucket9731/myweb-${BUILD_ID}.war .
                  curl -O https://downloads.apache.org/tomcat/tomcat-9/v9.0.71/bin/apache-tomcat-9.0.71.tar.gz
                  sudo tar -xvf apache-tomcat-9.0.71.tar.gz -C /opt/
                  sudo sh /opt/apache-tomcat-9.0.71/bin/shutdown.sh
                  sudo cp -rv myweb-${BUILD_ID}.war myweb.war
                  sudo cp -rv myweb.war /opt/apache-tomcat-9.0.71/webapps/
                  sudo sh /opt/apache-tomcat-9.0.71/bin/startup.sh
                  '''
                }
            } 
        }
    }
}

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
                   yum update -y
                   yum install awscli -y
                   aws configure set aws_access_key_id AKIAUSV5QX5HK6FOD37H
                   aws configure set aws_secret_access_key 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
                   aws configure set region us-east-1
                   aws s3 ls
                   mv /home/ec2-user/workspace/test-war/target/myweb.war /home/ec-user/myweb-${BUILD_ID}.war
                   aws s3 cp /home/ec2-user/myweb-${BUILD_ID}.war s3://mybucket9731/
                '''
                }
            }
        
        stage('s3-to-tomcat') {
            steps{
                withCredentials([sshUserPrivateKey(credentialsId: 'ec2', keyFileVariable: 'ec2', usernameVariable: 'utkarsha')]) {
                   
                 sh'''
                  ssh -i ${ec2} -o StrictHostKeyChecking=no ec2-user@11.290.42.90<<EOF
                  sudo apt update -y
                  sudo apt install awscli -y  
                  aws configure set aws_access_key_id ZKIAG73GHB45JQ3DWX9Y
                  aws configure set aws_secret_access_key lgZH2g8s8gsg8s8ksb/72whbsff
                  aws s3 ls
                  aws s3 cp s3://war-file/myweb-${BUILD_ID}.war .
                  curl -O https://downloads.apache.org/tomcat/tomcat-9/v9.0.70/bin/apache-tomcat-9.0.70.tar.gz
                  sudo tar -xvf apache-tomcat-9.0.70.tar.gz -C /opt/
                  sudo sh /opt/apache-tomcat-9.0.70/bin/shutdown.sh
                  sudo cp -rv myweb-${BUILD_ID}.war myweb.war
                  sudo cp -rv myweb.war /opt/apache-tomcat-9.0.70/webapps/
                  sudo sh /opt/apache-tomcat-9.0.70/bin/startup.sh
                  '''
                }
            } 
        }
    }
}

pipeline {
    agent  {
        label 'slave'
    }
    stages {
        stage('pull repo') {
            steps {
                git branch: 'main', url: 'https://github.com/Utkarshapatil123/onlinebookstore.git'
                
            }
        }
        stage('Building war') {
            steps {
                sh 'mvn clean package'
                sh 'mv target/*.war target/myweb.war'
            }
        }
        stage('pushing war to S3') {
            steps {
              sh'''
                   sudo apt update -y
                   sudo apt install awscli -y
                   aws configure set aws_access_key_id ZKIAG73GHB45JQ3DWX9Y
                   aws configure set aws_secret_access_key lgZH2g8s8gsg8s8ksb/72whbsff
                   aws configure set region ap-south-1
                   aws s3 ls
                   sudo mv /home/ec2-user/workspace/test-war/target/myweb.war /home/ec-user/myweb-${BUILD_ID}.war
                   aws s3 cp /home/ec2-user/myweb-${BUILD_ID}.war s3://war-file/
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

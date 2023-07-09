

pipeline {
  agent { 
  label 'ansible'
  }
  environment {
   AWS_EC2_PRIVATE_KEY=credentials('ec2-private-key') 
  }
  
  stages {
    
    //Get the Code from GitHub Repo
    stage('CheckOutCode'){
      steps{
        git credentialsId: 'daf39347-537f-42a2-a26e-1ea791c388c2', url: 'https://github.com/irfan313pathan/jekins-ansible-dynimc-inv.git'
      }
    }
     
    //Using Terrafrom can create the Servers
    
    stage('CreateServers'){
      steps{
       sh "terraform  -chdir=terraformscripts init"
       sh "terraform  -chdir=terraformscripts apply --auto-approve"
      }
    }
    
    //Run the playbook
    stage('RunPlaybook') {
      steps {
        sh "whoami"
        //List the dymaic inventory just for verification
        sh "ansible-inventory --graph -i inventory/aws_ec2.yaml"
        //Run playbook using dynamic inventory & limit exuection only fo tomcatservers.
        sh "ansible-playbook -i inventory/aws_ec2.yaml  playbooks/tomcat-setup.yaml -u ec2-user --private-key=$AWS_EC2_PRIVATE_KEY --limit tomcatservers --ssh-common-args='-o StrictHostKeyChecking=no'"
      }
    }
  
  }//stages closing
}//pipeline closing

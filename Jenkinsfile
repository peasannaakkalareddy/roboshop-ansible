pipeline {

  agent {
    node {
      label 'workstation'
    }
  }

  parameters {
    choice(name: 'env', choices: ['dev', 'prod'], description: 'Pick environment')
    string(name: 'component', defaultValue: '', description: 'component name')
     string(name: 'app_version', defaultValue: '', description: 'App Version to deploy')
  }

  stages {

    stage('Update Parameter Store') {
      steps {
        sh 'aws ssm put-parameter --name "${env}.${component}.app_version" --type "String" --value "${app_version}" --overwrite'
      }
    }

    stage('Ansible') {
      steps {
        sh 'aws ec2 describe-instances --filters Name=tag:Name,Values=${component}-${env} Name=instance-state-name,Values=running --query \'Reservations[*].Instances[*].PrivateIpAddress\' --output text >/tmp/inv'
        sh 'ansible-playbook -i /tmp/inv roboshop.yml -e ansible_user=centos -e ansible_password=DevOps321 -e role_name=${component} -e env=${env}'
      }
    }

  }

  post {
    always {
      cleanWs()
    }
  }

}
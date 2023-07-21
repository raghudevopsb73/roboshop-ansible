pipeline {
  agent {
    node {
      label 'workstation'
    }
  }

  options {
    ansiColor('xterm')
  }

  parameters {
    string(name: 'COMPONENT', defaultValue: '', description: 'Which Component')
    string(name: 'ENV', defaultValue: '', description: 'Which Env')
    string(name: 'APP_VERSION', defaultValue: '', description: 'Which Version')

  }

  stages {

    stage('Update Parameters') {
      steps {
        sh 'aws ssm put-parameter --name roboshop.${ENV}.${COMPONENT}.app_version --type "String" --value "${APP_VERSION}" --overwrite'
      }
    }

    stage('Get Servers') {
      steps {
        sh 'aws ec2 describe-instances --filters "Name=tag:Name,Values=${COMPONENT}-${ENV}" --query "Reservations[*].Instances[*].PrivateIpAddress" --output text |xargs -n1>inv'
      }
    }

    stage('Deploy Application') {
      steps {
        sh 'ansible-playbook -i inv main.yml  -e role_name=${COMPONENT} -e env=${ENV} -e app_version=${APP_VERSION} -e ansible_user=centos -e ansible_password=DevOps321 -e "ansible_ssh_timeout=30"'
      }
    }

  }



}
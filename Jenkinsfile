pipeline {
    agent { label 'ansible-node' }

    parameters {
        choice(
            name: 'ACTION',
            choices: ['deploy', 'rollback'],
            description: 'Choose deployment action'
        )
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Deploy using Ansible') {
            when {
                expression { params.ACTION == 'deploy' }
            }
            steps {
                sh '''
                  ansible --version
                  ansible-playbook -i inventory/hosts playbook/deploy.yaml
                '''
            }
        }

        stage('Rollback') {
            when {
                expression { params.ACTION == 'rollback' }
            }
            steps {
                sh '''
                  PREV=$(ls -dt /var/www/html/releases/* | sed -n '2p')
                  if [ -z "$PREV" ]; then
                    echo "No previous release found"
                    exit 1
                  fi
                  sudo ln -sfn $PREV /var/www/html/current
                  sudo systemctl reload nginx
                  echo "Rolled back to $PREV"
                '''
            }
        }
    }
}

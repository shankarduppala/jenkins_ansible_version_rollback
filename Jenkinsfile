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
        stage('Checkout SCM') {
            steps {
                git branch: 'main',
                url: 'https://github.com/shankarduppala/jenkins_ansible_version_rollback.git',
                credentialsId: 'Git-Creds'
            }
        }

        stage('Deploy using ansible') {
            when {
                expression { params.ACTION == 'deploy' }
            }
            steps {
                sh '''
                  ansible-playbook -i inventory/hosts playbook/deploy.yaml
                '''
            }
        }

        stage ('Rollback') {
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
                  ln -sfn $PREV /var/www/html/current
                  sudo systemctl reload nginx
                  echo 'Rolled back to $PREV'
                '''
            }
        }
    }
}

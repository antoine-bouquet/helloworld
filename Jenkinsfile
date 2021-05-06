
pipeline {
    agent none
    stages {
        stage('deploy with ansible') {
            agent { docker { image 'dirane/docker-ansible:latest' } }
            steps {
                script {

                    sh '''
                        cd ansible
                        ansible-playbook -i prod.yml helloworld.yml
                        '''
                }
            }
        }
        stage('test the deployment') {
            agent { docker { image 'dirane/docker-ansible:latest' } }
            steps {
                script {

                    sh '''
                        cd ansible
                        ansible-playbook -i prod.yml test.yml
                        '''
                }
            }
        }
    }
}

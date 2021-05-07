
pipeline {
    environment {
        IMAGE_NAME = "helloworld"
        IMAGE_TAG = "latest"
        STAGING = "antoine-miniprojet-staging"
        PRODUCTION = "antoine-miniprojet-production"
        IMAGE_REPO = "antoinebouquet1010"
     }
    agent none
    stages {
      stage('Build Image') {
            agent { docker { image 'docker' } }
            steps {
                script {
                    echo 'Building..'
                    sh 'docker build -t ${IMAGE_REPO}/${IMAGE_NAME}:${IMAGE_TAG} .'
                }
            }
        }
        stage('Run container based on builded image') {
            agent { docker { image 'docker' } }
            steps {
               script {
                 sh '''
                    docker run --name ${IMAGE_NAME} -d -p 80:80 ${IMAGE_REPO}/${IMAGE_NAME}:${IMAGE_TAG}
                    sleep 5
                 '''
               }
            }
        }
        stage('Test image') {
            agent { docker { image 'curlimages/curl' } }
            steps {
                script {
                        sh '''
                        curl http://172.17.0.1 | grep -q "Hello universe"
                        '''
              }
           }
        }
        stage('Cleaning Container') {
            agent { docker { image 'docker' } }
            steps {
                script {
                    sh 'docker rm -vf ${IMAGE_NAME}'
                }
            }
        }
        stage('Push image on dockerhub') {
           agent { docker { image 'docker' } }
           environment {
                DOCKERHUB_LOGIN = credentials('dockerhub_login_antoine')
            }
           steps {
               script {
                   sh '''
                   docker login --username ${DOCKERHUB_LOGIN_USR} --password ${DOCKERHUB_LOGIN_PSW}
                   docker push ${IMAGE_REPO}/${IMAGE_NAME}:${IMAGE_TAG}
                   '''
               }
           }
        }
stage('Push image in staging and deploy it') {
            when {
              expression { GIT_BRANCH == 'origin/master' }
            }
            agent any
            environment {
                HEROKU_API_KEY = credentials('heroku_api_key')
            }
            steps {
                script {
                    sh'''
                    heroku container:login
                    heroku create $STAGING || echo "project already exist"
                    heroku container:push -a $STAGING web
                    heroku container:release -a $STAGING web
                    '''
                }
            }
        }
        stage('Test Staging deployment') {
       when {
              expression { GIT_BRANCH == 'origin/master' }
            }
           agent { docker { image 'curlimages/curl' } }
           steps {
              script {
                    sh '''
                    curl https://${STAGING}.herokuapp.com | grep -q "Hello universe"
'''
              }
           }
      }
        stage('Push image in production and deploy it') {
            when {
              expression { GIT_BRANCH == 'origin/master' }
            }
            agent any
            environment {
                HEROKU_API_KEY = credentials('heroku_api_key')
            }
            steps {
                script {
                    sh'''
                    heroku container:login
                    heroku create $PRODUCTION || echo "project already exist"
                    heroku container:push -a $PRODUCTION web
                    heroku container:release -a $PRODUCTION web
                    '''
                }
            }
        }
        stage('Test Prod deployment') {
       when {
              expression { GIT_BRANCH == 'origin/master' }
            }
           agent { docker { image 'curlimages/curl' } }
           steps {
              script {
                    sh'''
                    curl https://${PRODUCTION}.herokuapp.com | grep -q "Hello universe"
                    '''
              }
           }
      }
}
}




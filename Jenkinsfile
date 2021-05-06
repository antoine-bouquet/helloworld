pipeline {
    environment {
        IMAGE_NAME = "helloworld"
        IMAGE_TAG = "latest"
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
            agent { docker { image 'docker' } }
            steps {
                script {
                    sh '''
                        curl http://172.17.0.1 | grep -q "Hello world"
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
                DOCKERHUB_LOGIN = credentials('ded4d165-8a67-471f-8797-a6a89d48abc9')
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
    }
}

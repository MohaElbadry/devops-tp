pipeline {
    agent any

    environment {
        IMAGE_NAME = "dockmoelbadry/devops-tp:latest"
        CONTAINER_NAME = "tp4-container"
    }

    stages {
        stage('Cloning Git') {
            steps {
                git branch: 'main', url: 'https://github.com/MohaElbadry/devops-tp.git'
            }
        }

        stage('Building image') {
            steps {
                sh 'docker build -t ${IMAGE_NAME} .'
            }
        }

        stage('Test image') {
            steps {
                sh 'docker run --rm ${IMAGE_NAME} cat /usr/share/nginx/html/index.html'
            }
        }

        stage('Publish Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                    docker login -u ${DOCKER_USER} -p ${DOCKER_PASS}
                    docker push ${IMAGE_NAME}
                    docker logout
                    '''
                }
            }
        }

        stage('Deploy image') {
            steps {
                sh '''
                docker stop ${CONTAINER_NAME} || true
                docker rm ${CONTAINER_NAME} || true
                docker run -d -p 8080:80 --name ${CONTAINER_NAME} ${IMAGE_NAME}
                '''
            }
        }
    }
}

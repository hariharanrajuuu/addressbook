pipeline {
    agent any

    environment {
        IMAGE_NAME = "hariharan0803/addressbook-app"
        CONTAINER_NAME = "addressbook-container"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'master',
                url: 'https://github.com/hariharanrajuuu/addressbook.git'
            }
        }

        stage('Build Maven') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:${BUILD_NUMBER} .'
                sh 'docker tag $IMAGE_NAME:${BUILD_NUMBER} $IMAGE_NAME:latest'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo $DOCKER_PASS | docker login \
                    -u $DOCKER_USER \
                    --password-stdin
                    '''
                }
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push $IMAGE_NAME:${BUILD_NUMBER}'
                sh 'docker push $IMAGE_NAME:latest'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                docker stop $CONTAINER_NAME || true
                docker rm $CONTAINER_NAME || true

                docker pull $IMAGE_NAME:latest

                docker run -d \
                  --name $CONTAINER_NAME \
                  -p 8081:8080 \
                  $IMAGE_NAME:latest
                '''
            }
        }
    }
}

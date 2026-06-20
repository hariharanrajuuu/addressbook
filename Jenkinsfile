pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "hariharan0803/addressbook-app"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/ImamKasali/addressbook.git'
            }
        }

        stage('Build WAR') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:latest .'
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: '6db80ad7-09a8-4d81-a46f-f91ae5c71f7c', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh 'docker push $DOCKER_IMAGE:latest'
                }
            }
        }

        stage('Deploy on EC2') {
            steps {
                sh 'docker rm -f addressbook || true'
                sh 'docker run -d -p 9090:8080 --name addressbook $DOCKER_IMAGE:latest'
            }
        }
    }
}

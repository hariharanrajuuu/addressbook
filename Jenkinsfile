pipeline {
    agent any

    stages {

        stage('Clone') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/hariharanrajuuu/addressbook.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t addressbook .'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                docker stop addressbook || true
                docker rm addressbook || true

                docker run -d \
                --name addressbook \
                -p 8081:8080 \
                addressbook
                '''
            }
        }
    }
}

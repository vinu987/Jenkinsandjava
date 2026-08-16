pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Verify WAR') {
            steps {
                sh 'ls -lh target/*.war'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t java-app:latest .'
            }
        }

        stage('Verify Docker Image') {
            steps {
                sh 'docker images java-app:latest'
            }
        }
    }
}
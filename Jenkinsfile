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

        stage('Login to ECR') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws-jenkins'
                ]]) {
                    sh '''
                        aws ecr-public get-login-password --region us-east-1 | \
                        docker login --username AWS --password-stdin public.ecr.aws
                    '''
                }
            }
        }

        stage('Push Image to ECR') {
            steps {
                sh '''
                    docker tag java-app:latest public.ecr.aws/a7f7g3y7/jenkinsecr:latest
                    docker push public.ecr.aws/a7f7g3y7/jenkinsecr:latest
                '''
            }
        }
    }
}
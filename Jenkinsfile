 // CI/CD webhook test
pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Unit Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Verify WAR') {
            steps {
                sh 'mvn package -DskipTests'
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

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                    kubectl set image deployment/java-app \
                    java-app=public.ecr.aws/a7f7g3y7/jenkinsecr:latest

                    kubectl rollout status deployment/java-app --timeout=120s
                '''
            }
        }

        stage('Verify Kubernetes Deployment') {
            steps {
                sh '''
                    kubectl get pods -o wide
                    kubectl get svc
                '''
            }
        }
    }
}

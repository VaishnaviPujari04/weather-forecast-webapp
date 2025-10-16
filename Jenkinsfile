pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-creds')
        IMAGE_NAME = "pvaishnavi27/yourproject"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'develop', credentialsId: 'github-creds', url: 'https://github.com/VaishnaviPujari04/weather-forecast-webapp.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'  // use npm build if Node.js
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'  // or npm test
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${IMAGE_NAME}:${env.BUILD_NUMBER} ."
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    sh """
                        echo ${DOCKERHUB_CREDENTIALS_PSW} | docker login -u ${DOCKERHUB_CREDENTIALS_USR} --password-stdin
                        docker push ${IMAGE_NAME}:${env.BUILD_NUMBER}
                        docker tag ${IMAGE_NAME}:${env.BUILD_NUMBER} ${IMAGE_NAME}:latest
                        docker push ${IMAGE_NAME}:latest
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f k8s/'  // runs only if you have kubeconfig setup
            }
        }
    }
}

pipeline {
    agent any

    tools {
        maven 'maven3.9'
    }

    environment {
        IMAGE_NAME = "hm-calculator"
        DOCKER_HOST_PORT = "7575"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/travvizzzz/helloworldtest.git'
            }
        }

        stage('Build') {
            steps {
                sh "mvn clean package -DskipTests"
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    env.IMAGE_TAG = "${BUILD_NUMBER}"
                    sh "docker build -t htetmyatisgod/calculator:2.0 ."
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'docker login -u $USER -p $PASS'
                    sh 'docker push htetmyatisgod/calculator:2.0'
                }
            }
        }

    } 

    post {
        always {
            echo "✅ travviiz pipeline finished."
        }

        success {
            echo "🎉 SUCCESS: App deployed successfully!"
        }

        failure {
            echo "❌ FAILURE: Check logs."
        }
    }
}
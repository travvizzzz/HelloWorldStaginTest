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
                git branch: 'main', url: 'https://github.com/travvizzzz/HelloWorldStaginTest.git'
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
                    sh "docker build -t htetmyatisgod/helloworld:2.0 ."
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
                    sh 'docker push htetmyatisgod/helloworld:2.0'
                }
            }
        }
        
        stage('Deploy to STAGING') {
    steps {
        withCredentials([file(credentialsId: 'kubeconfig-stagging', variable: 'KUBECONFIG')]) {
            sh '''
                export KUBECONFIG=$KUBECONFIG
                kubectl config current-context
                # Added the validation bypass flags below
                kubectl apply -f deployment.yaml -n staging --validate=false --insecure-skip-tls-verify=true
            '''
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
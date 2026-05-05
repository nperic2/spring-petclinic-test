pipeline {
    agent any

    environment {
        IMAGE_NAME = "spring-petclinic"
        IMAGE_TAG  = "${env.BUILD_NUMBER}"
    }

    stages {

        stage('Compile') {
            agent {
                docker { image 'maven:3.9.6-eclipse-temurin-17' }
            }
           steps {
                sh 'mvn clean compile -U -Dcheckstyle.skip=true'
            }
        }

        stage('Test') {
            agent {
                docker { image 'maven:3.9.6-eclipse-temurin-17' }
            }
            steps {
                sh 'mvn test -Dcheckstyle.skip=true'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        
        stage('Package & Build Docker Image') {
            steps {
                sh 'mvn package -DskipTests -Dcheckstyle.skip=true'
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                sh "docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest"
            }
        }
    }

    post {
        success {
            echo "✅ Build ${env.BUILD_NUMBER} succeeded. Image: ${IMAGE_NAME}:${IMAGE_TAG}"
        }
        failure {
            echo "❌ Build ${env.BUILD_NUMBER} failed."
        }
    }
}

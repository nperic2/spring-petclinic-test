pipeline {
    agent any

    environment {
        IMAGE_NAME = "spring-petclinic"
        IMAGE_TAG  = "${env.BUILD_NUMBER}"
    }

    tools {
        maven 'Maven-3.9'   // must match name configured in Jenkins
        jdk   'JDK-17'      // must match name configured in Jenkins
    }

    stages {

        stage('Compile') {
            steps {
                sh 'mvn clean compile -U'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Package & Build Docker Image') {
            steps {
                sh 'mvn package -DskipTests'
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                sh "docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest"
            }
        }

        // BONUS: Artifactory stage
        stage('Publish to Artifactory') {
            when {
                expression { return env.ARTIFACTORY_URL != null }
            }
            steps {
                rtMavenDeployer(
                    id: 'deployer',
                    serverId: 'artifactory-server',
                    releaseRepo: 'libs-release-local',
                    snapshotRepo: 'libs-snapshot-local'
                )
                rtMavenRun(
                    tool: 'Maven-3.9',
                    pom: 'pom.xml',
                    goals: 'install',
                    deployerId: 'deployer'
                )
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

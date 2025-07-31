//superior version with no jacoco failure
pipeline {
    agent any

    environment {
        SONAR_PROJECT_KEY = "java-ci-app"
    }

    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/fsl2023/my-java-app2.git', credentialsId: 'fsl2023'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
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

        stage('Code Coverage (JaCoCo)') {
            steps {
                sh 'mvn jacoco:report'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQubeServer') {
                    sh "mvn sonar:sonar -Dsonar.projectKey=${env.SONAR_PROJECT_KEY}"
                }
            }
        }

        stage('Quality Gate') {
            steps {
                waitForQualityGate abortPipeline: true
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t fsl2023/java-ci-app:v1 .'
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline completed successfully.'
        }
        failure {
            echo '❌ Pipeline failed. Check the logs above.'
        }
    }
}

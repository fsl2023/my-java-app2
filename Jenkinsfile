pipeline {
    agent any
    environment {
        SONAR_PROJECT_KEY = "java-ci-app"
    }
    stages {
        stage ('checkout') {
            steps {
                git url: 'https://github.com/fsl2023/my-java-app2.git', credentialsId: 'fsl2023'
            }
        }
        stage ('test & coverage') {
            steps {
                sh ' mvn clean test jacoco:report'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                    recordCoverage(tools: [jacoco(execPattern: 'target/jacoco.exec')])
                }
            }
        }
        stage('SonarQube analysis') {
            steps {
                withSonarQubeEnv('SonarQubeServer') {
                sh "mvn sonar:sonar -Dsonar.projectKey=${env.SONAR_PROJECT_KEY}"     
                }
            }
        }
        stage('quality gates') {
            steps {
                waitForQualityGate abortPipeline: true
            }
        }
        stage('build java app') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('docker build') {
            steps {
                sh "docker build -t fsl2023/java-ci-app:v1"
                sh "docker run -d fsl2023/java-ci-app:v1"
            }
        }
    }
}

pipeline {
    agent {
        kubernetes {
            yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: maven
spec:
  containers:
  - name: maven
    image: maven:3.8.2-adoptopenjdk-8
    resources:
      limits: {}
      requests:
        memory: "8192Mi"
        cpu: "1000m"
    command:
    - cat
    tty: true
        """.stripIndent()
            defaultContainer 'maven'
        }
    }
    options {
        timestamps()
        timeout(time: 10, unit: 'MINUTES')
        buildDiscarder(logRotator(daysToKeepStr: '30', artifactDaysToKeepStr: '30'))
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
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
        stage('Deliver') {
            steps {
                sh './jenkins/scripts/deliver.sh'
            }
        }
    }
}
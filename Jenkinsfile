pipeline {
    agent any

    stages {
        stage('clean workspace') {
            steps {
                cleanWs()
            }
        }
        
        stage('Checkout') {
            steps {
                git credentialsId: 'github-token', url: 'https://github.com/s5wesley/commercial-card1.git'
            }
        }
        stage('Check Java Version') {
            steps {
                sh 'java -version'
            }
        }

        stage('Compile') {
            steps {
                script {
                    // Compile the Java source code
                    sh 'mvn clean compile'
                }
            }
        }

        stage('Testing') {
            steps {
                script {
                    // Package the application
                    sh 'mvn test'
                }
            }
        }
        stage('Package') {
            steps {
                script {
                    // Package the application
                    sh 'mvn package'
                }
            }
        }
    }
    
    post {
        always {
            junit 'target/surefire-reports/*.xml'
        }
    }
}

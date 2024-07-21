pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git credentialsId: 'github-token', url: 'https://github.com/s5wesley/commercial-card1.git'
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
                    sh 'mvn package'
                }
            }
        }
    }
}

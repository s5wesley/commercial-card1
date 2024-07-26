pipeline {
    agent any

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }
        
        stage('Checkout') {
            steps {
                withCredentials([string(credentialsId: 'github-token', variable: 'GITHUB_TOKEN')]) {
                    sh 'git clone https://$GITHUB_TOKEN@github.com/s5wesley/commercial-card1.git'
                }
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
                    sh 'mvn test -DskipTests=true'
                }
            }
        }
        
        stage('File System Scan') {
            steps {
                sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }

        // stage('SonarQube Analysis') {
        //     agent {
        //         docker {
        //             image 'sonarsource/sonar-scanner-cli:4.8.0'
        //             args '-v /path/to/sonar-scanner:/opt/sonar-scanner' // Make sure this path is correctly set
        //         }
        //     }
        //     environment {
        //         CI = 'true'
        //         scannerHome = '/opt/sonar-scanner'
        //     }
        //     steps {
        //         withSonarQubeEnv('sonar') {
        //             sh "${scannerHome}/bin/sonar-scanner"
        //         }
        //     }
        // }
    }
}

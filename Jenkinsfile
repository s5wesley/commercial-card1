pipeline {
    agent { label 'dynamic-docker-agent' } // Use the dynamic Docker agent

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-cred')
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Clean Env') {
            steps {
                sh '''
                    docker system prune -fa || true
                '''
            }
        } 

        stage('Checkout') {
            steps {
                git branch: 'feature/wesley', credentialsId: 'github-credential', url: 'https://github.com/DEL-ORG/commercial-card.git'
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
                    sh 'mvn clean compile'
                }
            }
        }

        stage('Login') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }

        stage('Testing') {
            steps {
                script {
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

        stage('Build') {
            steps {
                sh "mvn package -DskipTests=true"
            }
        }

        stage('Build-images') {
            steps {
                sh '''
                    docker build -t bulawesley/card-svc:v$BUILD_NUMBER .
                '''
            }
        }

        stage('Docker Image Scan') {
            steps {
                sh "trivy image --format table -o trivy-image-report.html bulawesley/card-svc:v$BUILD_NUMBER"
            }
        }

        stage('Push-ui') {
            steps {
                sh 'docker push bulawesley/card-svc:v$BUILD_NUMBER'
            }
        }

        stage('Deploy Application') {
            steps {
                script {
                    // Stop any running container with the same name
                    sh 'docker stop commercial-card-app || true'
                    sh 'docker rm commercial-card-app || true'

                    // Run the new container with the application
                    sh '''
                        docker run -d \
                        --name commercial-card-app \
                        -p 8080:8080 \
                        bulawesley/card-svc:v$BUILD_NUMBER
                    '''
                }
            }
        }
    }
}

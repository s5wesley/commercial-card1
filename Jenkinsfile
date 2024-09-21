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
                git 'https://github.com/s5wesley/commercial-card1.git'
            }
        }

        stage('Check Java Version') {
            steps {
                sh 'java -version'
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Login') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }

        stage('Testing') {
            steps {
                sh 'mvn test -DskipTests=true'
            }
        }

        stage('File System Scan') {
            steps {
                sh 'trivy fs --format table -o trivy-fs-report.html .'
            }
        }

        // Uncomment and configure the following stage if you need SonarQube analysis
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
                sh 'mvn package -DskipTests=true'
            }
        }

        stage('Build-images') {
            steps {
                sh 'docker build -t bulawesley/card-svc:v$BUILD_NUMBER .'
            }
        }

        stage('Docker Image Scan') {
            steps {
                sh 'trivy image --format table -o trivy-image-report.html bulawesley/card-svc:v$BUILD_NUMBER'
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
                    sh 'docker stop commercial-card1 || true'
                    sh 'docker rm commercial-card1 || true'

                    // Run the new container with the application
                    sh '''
                        docker run -d \
                        --name commercial-card1 \
                        -p 4567:8080 \
                        bulawesley/card-svc:v$BUILD_NUMBER
                    '''
                }
            }
        }

        stage('helm-charts') {
            steps {
                script {
                    sh '''
                        rm -rf commercial-card1 || true
                        git clone https://github.com/s5wesley/commercial-card1.git
                        cd commercial-card1

                        cat << EOF > values.yaml
                        repository:
                          tag:  v$BUILD_NUMBER
                          assets:
                            image: bulawesley/card-svc
                        EOF

                        cat values.yaml
                        git add -A
                        git commit -m "Change from Jenkins build ${BUILD_NUMBER}"
                        git push 
                    '''
                }
            }
        }

        stage('Display Server IP') {
            steps {
                sh 'curl ifconfig.io'
            }
        }
    }
}

pipeline {
    agent any
    
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
        
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/surya3796/Valentine-Day-Project.git'
            }
        }
        
        stage('Trivy Filesystem Scan') {
            steps {
                sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }
        
        stage('Sonarqube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh "$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=Valentine -Dsonar.projectName=Valentine"
                }
            }
        }
        
        stage('Build & Tag Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker build -t surya030596/valentine:latest ."
                    }
                }
            }
        }
        
        stage('Trivy Image Scan') {
            steps {
                sh "trivy image --format json -o trivy-image-report.json surya/valentine:latest"
            }
        }
        
        stage('Push Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker push surya030596/valentine:latest"
                    }
                }
            }
        }
        
        stage('Deploy to Container') {
            steps {
                sh "docker run -d -p 8081:80 surya030596/valentine:latest"
            }
        }
        
        stage('Deployment Done') {
            steps {
                echo 'Deployment is Successful'
            }
        }
    }
}

pipeline {
    agent any
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }
    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout from Git') {
            steps {
                git branch: 'main', url: 'https://github.com/abuzarkhan1/Devops-Eks-ArgoCD.git'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.projectName=Netflix \
                    -Dsonar.projectKey=Netflix'''
                }
            }
        }
        stage('Trivy File System Scan') {
            steps {
                script {
                    try {
                        sh "trivy fs . > trivyfs.txt"
                    } catch (Exception e) {
                        echo "Trivy FS scan failed: ${e.message}"
                    }
                }
            }
        }
        stage('Docker Build Image') {
            steps {
                sh "docker build --build-arg API_KEY=794cee3ccdc629933512fc84db90cd51 -t netflix1 ."
            }
        }
        stage('Trivy Image Scan') {
            steps {
                sh "trivy image netflix1 > trivyimage.txt"
            }
        }
        stage('Docker Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker tag netflix1 abuzarkhan1/netflix1:latest"
                        sh "docker push abuzarkhan1/netflix1:latest"
                    }
                }
            }
        }
    }
}

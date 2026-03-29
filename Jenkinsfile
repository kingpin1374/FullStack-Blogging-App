pipeline {
    agent any

    tools {
        jdk 'jdk17'
        maven 'maven3'
    }

    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        MAVEN_OPTS = "-Xmx512m"
        // Replace with your Docker Hub/ECR repository
        DOCKER_IMAGE = "kingpin1374/blogging-app:${BUILD_NUMBER}"
        EKS_CLUSTER_URL = "https://EC2E9A88ED3A2DBAABFB58DC00D0F6E9.gr7.ap-south-1.eks.amazonaws.com"
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', 
                    credentialsId: 'github', 
                    url: 'https://github.com/kingpin1374/FullStack-Blogging-App.git'
            }
        }

        stage('Compile') {
            steps {
                sh "mvn clean compile"
            }
        }

        stage('Test') {
            steps {
                sh "mvn test"
            }
        }

        stage('Trivy FS Scan') {
            steps {
                sh "trivy fs --format table -o fs.html ."
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh """
                        ${SCANNER_HOME}/bin/sonar-scanner \
                        -Dsonar.projectName=Blogging-app \
                        -Dsonar.projectKey=Blogging-app \
                        -Dsonar.java.binaries=target/classes
                    """
                }
            }
        }

        stage('Build & Package') {
            steps {
                sh "mvn package -DskipTests"
            }
        }

        stage('Deploy to Nexus') {
            steps {
                withMaven(
                    globalMavenSettingsConfig: 'maven-settings', 
                    jdk: 'jdk17', 
                    maven: 'maven3', 
                    traceability: true
                ) {
                    sh "mvn deploy -DskipTests"
                }
            }
        }

        // --- NEW: Docker Build Stage (Required for K8s) ---
        stage('Docker Build & Push') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-cred', passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {
                        sh "docker build -t ${DOCKER_IMAGE} ."
                        sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                        sh "docker push ${DOCKER_IMAGE}"
                    }
                }
            }
        }

        stage('K8s Deploy') {
            steps {
                // Using the standard Kubernetes CLI plugin syntax
                withKubeConfig(credentialsId: 'k8-cred', serverUrl: "${EKS_CLUSTER_URL}") {
                    sh "kubectl apply -f deployment-service.yml"
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                withKubeConfig(credentialsId: 'k8-cred', serverUrl: "${EKS_CLUSTER_URL}") {
                    // Wait for the rollout to ensure pods are actually ready
                    sh "kubectl rollout status deployment/blogging-app-deployment --timeout=90s"
                    sh "kubectl get pods"
                    sh "kubectl get svc"
                }
            }
        }
    }
    
    post {
        always {
            echo 'Cleaning up workspace...'
            cleanWs()
        }
    }
}

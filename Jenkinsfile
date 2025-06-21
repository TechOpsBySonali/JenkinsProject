pipeline {
    agent any

    environment {
        PROJECT_ID = 'your-project-id'
        CLUSTER_NAME = 'my-cluster'
        LOCATION = 'us-central1-a'
        CREDENTIALS_ID = 'your-credentials-id'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/spring-projects/spring-petclinic.git'
            }
        }

        stage('Build') {
            steps {
                sh './mvnw clean package'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t gcr.io/$PROJECT_ID/petclinic:latest .'
            }
        }

        stage('Push to Container Registry') {
            steps {
                withCredentials([string(credentialsId: CREDENTIALS_ID, variable: 'TOKEN')]) {
                    sh 'docker login -u _json_key --password-stdin https://gcr.io < $TOKEN'
                    sh 'docker push gcr.io/$PROJECT_ID/petclinic:latest'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                kubernetesDeploy(
                    configs: 'k8s/deployment.yaml',
                    kubeconfigId: CREDENTIALS_ID,
                    enableConfigSubstitution: true,
                    namespace: 'default'
                )
            }
        }
    }
}

pipeline {
    agent any
    stages {
        stage('Pull Code From GitHub') {
            steps {
                git 'https://github.com/John241198/Mazeball.git'
            }
        }
        stage('Build the Docker image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        sh 'docker build -t mazeimage .'
                        sh 'docker tag mazeimage jsnov24/mazeimage:latest'
                        sh 'docker tag mazeimage jsnov24/mazeimage:${BUILD_NUMBER}'
                    }
                }
            }
        }
        stage('Trivy Vulnerability Scan') {
            steps {
                script {
                    sh 'trivy image jsnov24/mazeimage:latest > trivy.json'
                    sh 'trivy image jsnov24/mazeimage:${BUILD_NUMBER} >> trivy.json'
                }
            }
        }
        stage('Push the Docker image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker', toolName: 'docker') {
                        sh 'docker image push jsnov24/mazeimage:latest'
                        sh 'docker image push jsnov24/mazeimage:${BUILD_NUMBER}'
                    }
                }
            }
        }
        stage('Deploy on Kubernetes') {
            steps {
                sh 'kubectl apply -f /home/vijay/workspace/game-project/pod.yml'
                sh 'kubectl rollout restart deployment loadbalancer-pod'
            }
        }
    }
}

pipeline {
    agent { label 'k8s-server' }
    stages {
        stage('Pull Code From GitHub') {
            steps {
                git 'https://github.com/John241198/Mazeball.git'
            }
        }
        stage('Build the Docker image') {
            steps {
                sh 'sudo docker build -t mazeimage .'
                sh 'sudo docker tag mazeimage jsnov24/mazeimage:latest'
                sh 'sudo docker tag mazeimage jsnov24/mazeimage:${BUILD_NUMBER}'
            }
        }
        stage('Trivy Vulnerability Scan') {
            steps {
                script {
                    sh 'sudo trivy image jsnov24/mazeimage:latest'
                    sh 'sudo trivy image jsnov24/mazeimage:${BUILD_NUMBER}'
                }
            }
        }
        stage('Push the Docker image') {
            steps {
                sh 'sudo docker image push jsnov24/mazeimage:latest'
                sh 'sudo docker image push jsnov24/mazeimage:${BUILD_NUMBER}'
            }
        }
        stage('Deploy on Kubernetes') {
            steps {
                sh 'sudo kubectl apply -f /home/vijay/workspace/game-project/pod.yml'
                sh 'sudo kubectl rollout restart deployment loadbalancer-pod'
            }
        }
    }
}

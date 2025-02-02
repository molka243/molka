def commit_id
pipeline {
    agent any

    stages {
        stage('Preparation') {
            steps {
                checkout scm
                sh 'git rev-parse --short HEAD > .git/commit-id'
                script {
                    commit_id = readFile('.git/commit-id').trim()
                }
            }
        }
        stage('Build') {
            steps {
                echo 'Building....'
                sh 'scp -r -i $(minikube ssh-key) ./.* docker@$(minikube ip):~/'
                sh 'minikube ssh "docker build -t webapp:${commit_id} ."'
                echo 'build complete'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying to Kubernetes'
                sh "sed -i -r 's|richardchesterwood/k8s-fleetman-webapp-angular:release0-5|webapp:${commit_id}|' deployment.yaml"
                sh 'kubectl get all'
                sh 'kubectl apply -f deployment.yaml'
                sh 'kubectl get all'
                echo 'deployment complete'
            }
        }
    }
}

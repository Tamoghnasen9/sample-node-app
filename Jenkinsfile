pipeline {

    agent {
        label 'docker'
        }

    environment {
                DOCKERHUB_CREDENTIALS=credentials('dockerhub-cred-personal')
            }  

    stages {

        stage('Build and Test Application') {
            agent {
                docker {
                    image 'node:latest'
                    args '-u root:root -v /home/jenkins/.ssh:/root/.ssh'
                }
            }
            stages {
                stage('Install Dependencies') {
                    steps {
                        sh 'npm install'
                        }
                    }
                stage('Run Tests') {
                    steps {
                        sh 'npm test'
                        }
                    }
                }
            }

        stage('Build and Push image') {
            steps {
                sh 'docker build . -t $DOCKERHUB_CREDENTIALS_USR/node-sample-app:latest'
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                sh 'docker push $DOCKERHUB_CREDENTIALS_USR/node-sample-app:latest'
            }
        }

        stage('Connect to Kubernetes cluster and deploy') {
            agent {
                docker {
                    image 'bitnami/kubectl:latest'
                    args '-u root:root -v /home/jenkins/.ssh:/root/.ssh'
                }
            }
            steps {
                // Todo: Authenticate to Kubernetes cluster
                sh 'kubectl apply -f manifest/app.yaml'
            }
        }    
    }
}
pipeline {
    agent any
    environment {
        PROJECT_ID = 'allcare-systems'
        CLUSTER_NAME = 'allcare-dev'
        LOCATION = 'asia-southeast2-a'
        CREDENTIALS_ID = 'allcare-gke'
    }
    stages {
        stage("Checkout code") {
            steps {
                checkout scm
            }
        }
        stage("Build image") {
            steps {
                script {
                    myapp = docker.build("asia.gcr.io/allcare-systems/docker-test:${env.BUILD_ID}")
                }
            }
        }
        stage("Push image") {
            steps {
                script {
                    docker.withRegistry('https://asia.gcr.io', 'gcr:allcare-gcr') {
                            myapp.push("latest")
                            myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }        
      stage('Deploy to GKE') {
            steps{
                sh "sed -i 's/docker-test:latest/docker-test:${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder',namespace: 'yakes', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        }
    }    
}

                

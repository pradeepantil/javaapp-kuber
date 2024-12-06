pipeline {
    
    agent {
        label 'linux'
    }
    
    tools {
        maven "MVN3"
        dockerTool "docker"
    }
    
    stages {
        stage('pullscm') {
            steps {
                git credentialsId: 'github', url: 'git@github.com:pradeepantil/javaapp-kuber.git'
            }
        }
        
        stage('build') {
            steps {
                sh "mvn -f kubernetes-java clean install"
            }
        }
        
        stage('build docker image') {
            steps {
                script {
                    dockerImage = docker.build("pradeep1065/javaapp-k8s","kubernetes-java")
                }
            }
        }
        
        stage('Push docker image') {
            steps {
                script {
                    docker.withRegistry( '', 'dockerhub') {
                        dockerImage.push ("$BUILD_NUMBER")
                    }
                }
            }
        }
        stage ('Validate k8s connectivity') {
            steps {
                script {
                    withKubeConfig([credentialsId: 'kubeconfig']) {
                        sh "sed -i s/latest/$BUILD_NUMBER/g kubernetes-java/deploy.yml"
                        sh "kubectl apply -f kubernetes-java/deploy.yml"
                        sh "sleep 20 && kubectl get svc"
                    }
                }
            }
        }
    }
}

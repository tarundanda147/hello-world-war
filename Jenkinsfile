pipeline {
    agent any

    stages {
        stage('checkout') {
            steps {
                sh 'rm -rf hello-world-war'
                sh 'git clone https://github.com/tarundanda147/hello-world-war/'
            }
        }
        
        stage('build') {
            steps {
                dir("hello-world-war") {
                    sh 'echo "inside build"'
                    sh "docker build -t tomcat-war:${BUILD_NUMBER} ."
                }
            }
        }
        
        stage('push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'aed771cc-a7bd-45dc-b4ab-c30d8db5faac', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
                    sh "docker tag tomcat-war:${BUILD_NUMBER} tarundanda147/tomcat:${BUILD_NUMBER}"
                    sh "docker push tarundanda147/tomcat:${BUILD_NUMBER}"
                }
            }
        }
        stage('Helm Deploy') {
    steps {
        kubernetesDeploy(
            configs: '', 
            dockerCredentials: [
                [credentialsId: 'aed771cc-a7bd-45dc-b4ab-c30d8db5faac', url: 'https://hub.docker.com/repository/docker/tarundanda147/tomcat']
            ], 
            kubeConfig: [path: ''], 
            kubeconfigId: 'eks-cluster', 
            secretName: '', 
            secretNamespace: 'hello-world-war', 
            ssh: [sshCredentialsId: '*', sshServer: ''], 
            textCredentials: [
                certificateAuthorityData: '', 
                clientCertificateData: '', 
                clientKeyData: '', 
                serverUrl: 'https://'
            ]
        ) {
            echo 'Deploying to Kubernetes using Helm'
            sh "helm upgrade first --install hello-world-war --namespace hello-world-war --set image.tag=$BUILD_NUMBER"
        }
    }
}


        }
    }


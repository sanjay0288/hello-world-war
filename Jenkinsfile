pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                sh 'docker build -t tomcat-war:${BUILD_NUMBER} .'
            }
        }
        
        stage('Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: '1207a9ab-d7c7-4b11-a923-b1a116622af2', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
                    sh "docker tag tomcat-war:${BUILD_NUMBER} sanjay0288/tomcat:${BUILD_NUMBER}"
                    sh "docker push sanjay0288/tomcat:${BUILD_NUMBER}"
                }
            }
        }
        
        stage('Deploy') {
            parallel {
                stage('DeployQA') {
                    agent { label 'slave1' }
                    steps {
                        script {
                            deployToNode('slave1')
                        }
                    }
                }
                stage('DeployProd') {
                    agent { label 'slave2' }
                    steps {
                        script {
                            deployToNode('slave2')
                        }
                    }
                }
            }
        }
    }
}

def deployToNode(nodeLabel) {
    withCredentials([usernamePassword(credentialsId: '1207a9ab-d7c7-4b11-a923-b1a116622af2', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
        sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
        sh "docker pull sanjay0288/tomcat:${BUILD_NUMBER}"
        sh "docker rm -f tomcat-${nodeLabel} || true"
        sh "docker run -d -p 5555:8080 --name tomcat-${nodeLabel} sanjay0288/tomcat:${BUILD_NUMBER}"
    }
}

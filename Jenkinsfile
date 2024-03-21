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
                withCredentials([usernamePassword(credentialsId: '9edb749c-52c9-40d2-9266-024789f72979', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
                    sh "docker tag tomcat-war:${BUILD_NUMBER} sanjay0288/tomcat:${BUILD_NUMBER}"
                    sh "docker push sanjay0288/tomcat:${BUILD_NUMBER}"
                }
            }
        }
        
        stage('Deploy') {
            parallel {
                stage('DeployQA') {
                    agent { label 'slave102' }
                    steps {
                        script {
                            deployToNode('slave102')
                        }
                    }
                }
                stage('DeployProd') {
                    agent { label 'slave33' }
                    steps {
                        script {
                            deployToNode('slave33')
                        }
                    }
                }
            }
        }
    }
}

def deployToNode(nodeLabel) {
    withCredentials([usernamePassword(credentialsId: '9edb749c-52c9-40d2-9266-024789f72979', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
        sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
        sh "docker pull sanjay0288/tomcat:${BUILD_NUMBER}"
        sh "docker rm -f tomcat-${nodeLabel} || true"
        sh "docker run -d -p 5555:8080 --name tomcat-${nodeLabel} sanjay0288/tomcat:${BUILD_NUMBER}"
    }
}

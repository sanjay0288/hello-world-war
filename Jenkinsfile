pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                sh 'rm -rf hello-world-war'
                sh 'git clone https://github.com/sanjay0288/hello-world-war.git'
            }
        }

        stage('Build') {
            steps {
                script {
          

                    sh "mvn clean install"
                }
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                script {
                    def warFileName = "dist/hello-world.war"
                    def tomcatDir = "/opt/apache-tomcat-10.1.24"

                    sh "cp ${warFileName} ${tomcatDir}/webapps/"
                    sh "${tomcatDir}/bin/shutdown.sh"
                    sleep 5 
                    sh "${tomcatDir}/bin/startup.sh"
                    sleep 10
                    }
                
            }
        }
    }

    post {
        success {
            echo "Deployment to Tomcat successful!"
        }
        failure {
            echo "Deployment to Tomcat failed!"
        }
    }
}

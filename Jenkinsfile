sImageMaven = 'maven:3-alpine'
sAgentLabel = 'wsl2'
pipeline {
    agent none
    stages {
        stage('Build') {
            agent {
                docker {
                    label     sAgentLabel
                    image     'maven:3-alpine'
                    args      '-v $WORKSPACE/.m2:/root/.m2'
                    reuseNode true
                }
            }
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
            agent {
                docker {
                    label     sAgentLabel
                    image     'maven:3-alpine'
                    args      '-v $WORKSPACE/.m2:/root/.m2'
                    reuseNode true
                }
            }
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Deliver') {
            steps {
                sh './jenkins/scripts/deliver.sh'
            }
        }
    }
}

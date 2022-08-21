sAgentLabel = 'wsl2'
sImageMaven = 'maven:3-alpine'
pipeline {
    agent {
        label sAgentLabel
    }
    stages {
        agent {
            docker {
                label sAgentLabel
                image 'maven:3-alpine'
                args  '-v $WORKSPACE/.m2:/root/.m2'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
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

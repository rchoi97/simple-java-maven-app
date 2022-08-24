sImageMaven = 'maven:3-alpine'
sAgentLabel = 'wsl2'
pipeline {
    agent { label sAgentLabel }
    stages {
        stage('Prepare') {
            steps {
                sh """
                    echo "sAgentLabel:$sAgentLabel" | true
                    echo "env.sAgentLabel:${env.sAgentLabel}" | true
                """
            }
        }
        stage('Build') {
            agent {
                docker {
                    // label     sAgentLabel
                    image     'maven:3-alpine'
                    args      "-v ${WORKSPACE}/.m2:/root/.m2"
                    reuseNode true
                }
            }
            steps {
                sh """
                    hostname | true
                    hostname -i | true
                    hostname -I | true
                    pwd
                    ps -elf
                    id 
                    mvn -B -DskipTests clean package
                    sh -c 'timeout -t 3 ping google.com'
                """
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
            agent {
                docker {
                    label     sAgentLabel
                    image     'maven:3-alpine'
                    args      '-v $WORKSPACE/.m2:/root/.m2'
                    reuseNode true
                }
            }
            steps {
                sh '''
                  ./jenkins/scripts/deliver.sh
                '''
            }
        }
    }
}

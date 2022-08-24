// sImageMaven = 'maven:3-alpine'
// with env.V, both env.V and V works
// with V, only V works (env.V does not)
// env.sAgentLabel = 'wsl2'
pipeline {
    agent { label sAgentLabel }
    environment {
        sAgentLabel = 'wsl2'
        sImageMaven = 'maven:3-alpine'
    }
    stages {
        stage('Prepare') {
            steps {
                sh """
                    ### both works
                    echo "sAgentLabel:$sAgentLabel" | true
                    echo "braced-sAgentLabel:${sAgentLabel}" | true
                    ### this is null
                    echo "env.sAgentLabel:${env.sAgentLabel}" | true
                    ### both works
                    echo "BUILD_ID:$BUILD_ID" | true
                    echo "env.BUILD_ID:${env.BUILD_ID}" | true
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
                    sh -c 'timeout -t 3 ping google.com' | true
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

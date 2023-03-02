pipeline {
    agent any

    stages {
        stage('Checkout Repo') {
            steps {
                sh "checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: '2015f835-5756-4d8f-86c2-c468cc526883', url: 'https://github.com/aryandvn/Nexus.git']])"
            }
        }
        stage('Build') {
            steps {
                sh "mvn clean package"
            }
        }
    }
}

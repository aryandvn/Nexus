pipeline {
    agent any
    
    tools {
        maven "MAVEN"
    }

    stages {
        stage('Checkout Repo') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: '2015f835-5756-4d8f-86c2-c468cc526883', url: 'https://github.com/aryandvn/Nexus.git']])
            }
        }
        stage('Static Analysis') {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    withSonarQubeEnv('sq1') {
                        sh """
                        #git clone https://github.com/aryandvn/New_Repo.git
                        sudo docker run --rm -e SONAR_HOST_URL="http://10.12.124.93:9000/" -e SONAR_SCANNER_OPTS="-Dsonar.projectKey=Jenkins_Sonar" -e SONAR_LOGIN="sqp_7dca9780bce088b96a80c72a736afd5d48c86162" -v "/var/lib/jenkins/workspace/Bar:/usr/src" sonarsource/sonar-scanner-cli"""
                        }
                    }
            }
        }
        stage('Build') {
            steps {
                sh "mvn clean package"
            }
        }
        stage("Publish to Nexus Repository Manager") {
            steps {
                script {
                    pom = readMavenPom file: "pom.xml";
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    artifactPath = filesByGlob[0].path;
                    artifactExists = fileExists artifactPath;
                    if(artifactExists) {
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                        nexusArtifactUploader(
                            nexusVersion: 'nexus3',
                            protocol: 'http',
                            nexusUrl: '10.12.124.93:8081',
                            groupId: 'pom.com.mycompany.app',
                            version: 'pom.1.0-SNAPSHOT',
                            repository: 'maven-repo',
                            credentialsId: 'NEXUS',
                            artifacts: [
                                [artifactId: 'pom.my-app',
                                classifier: '',
                                file: artifactPath,
                                type: pom.packaging],
                                [artifactId: 'pom.my-app',
                                classifier: '',
                                file: "pom.xml",
                                type: "pom"]
                            ]
                        );
                    } else {
                        error "*** File: ${artifactPath}, could not be found";
                    }
                }
            }
        }
    }
}

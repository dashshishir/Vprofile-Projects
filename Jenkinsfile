pipeline{
    agent any

    tools{
         maven "maven3"
    }  

    environment{
        SNAP_REPO= 'vprofile-snapshot'
        NEXUS_USER= 'admin'
        NEXUS_PASS= 'Vprofile@01'
        RELEASE_REPO= 'vprofile-maven-release'
        CENTRAL_REPO= 'vprofile-maven-central'
        NEXUS_GRP_REPO= 'vprofile-maven-group'
        NEXUSIP= '172.31.90.236'
        NEXUSPORT= '8081'
        NEXUS_LOGIN ='nexuslogin'
        SONARSERVER= 'sonarserver'
        SONARSCANNER= 'sonarscanner'

    }

    stages{
        stage('BUILD'){
            steps{
                sh 'mvn -s settings.xml -DskipTests install'
            }
            post{
                success {
                    echo 'Now-Archiving'
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }

        stage('Test'){
            steps{
                sh 'mvn test'
            }
        }

        stage('Integration Tests'){
            steps{
                sh 'mvn verify -DskipUnitTests'
            }
            
        }

        stage('CODE ANALYSIS WITH CHECKSTYLE'){
            steps{
                sh 'mvn checkstyle:checkstyle'
            }
        }

        stage('Code analysis with Sonar Scanner'){

            environment{
                scannerHome = tool "${SONARSCANNER}"
            }

            steps {
                 withSonarQubeEnv("${SONARSERVER}") {
                    sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                   -Dsonar.projectName=vprofile-repo \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml''' 
                }

                
            }
        }

        stage('upload artifacts to nexus'){
            steps{
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: "${NEXUSIP}:${NEXUSPORT}",
                    groupId: 'QA',
                    version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                    repository: "${RELEASE_REPO}",
                    credentialsId: "${NEXUS_LOGIN}",
                    artifacts: [

                        [artifactId: 'vprofileapp',
                        classifier: '',
                        file: 'target/vprofile-v2.war',
                        type: "war"]

                    ]
                )
        }
        
    }
}
}
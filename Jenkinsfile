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
    }
}
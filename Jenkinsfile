// pipeline {
    
// 	agent any
// /*	
// 	tools {
//         maven "maven3"
//     }
// */	
//     environment {
//         NEXUS_VERSION = "nexus3"
//         NEXUS_PROTOCOL = "http"
//         NEXUS_URL = "172.31.40.209:8081"
//         NEXUS_REPOSITORY = "vprofile-release"
// 	    NEXUS_REPOGRP_ID    = "vprofile-grp-repo"
//         NEXUS_CREDENTIAL_ID = "nexuslogin"
//         ARTVERSION = "${env.BUILD_ID}"
//     }
	
//     stages{
        
//         stage('BUILD'){
//             steps {
//                 sh 'mvn clean install -DskipTests'
//             }
//             post {
//                 success {
//                     echo 'Now Archiving...'
//                     archiveArtifacts artifacts: '**/target/*.war'
//                 }
//             }
//         }

// 	stage('UNIT TEST'){
//             steps {
//                 sh 'mvn test'
//             }
//         }

// 	stage('INTEGRATION TEST'){
//             steps {
//                 sh 'mvn verify -DskipUnitTests'
//             }
//         }
		
//         stage ('CODE ANALYSIS WITH CHECKSTYLE'){
//             steps {
//                 sh 'mvn checkstyle:checkstyle'
//             }
//             post {
//                 success {
//                     echo 'Generated Analysis Result'
//                 }
//             }
//         }

//         stage('CODE ANALYSIS with SONARQUBE') {
          
// 		  environment {
//              scannerHome = tool 'sonarscanner4'
//           }

//           steps {
//             withSonarQubeEnv('sonar-pro') {
//                sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
//                    -Dsonar.projectName=vprofile-repo \
//                    -Dsonar.projectVersion=1.0 \
//                    -Dsonar.sources=src/ \
//                    -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
//                    -Dsonar.junit.reportsPath=target/surefire-reports/ \
//                    -Dsonar.jacoco.reportsPath=target/jacoco.exec \
//                    -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
//             }

//             timeout(time: 10, unit: 'MINUTES') {
//                waitForQualityGate abortPipeline: true
//             }
//           }
//         }

//         stage("Publish to Nexus Repository Manager") {
//             steps {
//                 script {
//                     pom = readMavenPom file: "pom.xml";
//                     filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
//                     echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
//                     artifactPath = filesByGlob[0].path;
//                     artifactExists = fileExists artifactPath;
//                     if(artifactExists) {
//                         echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version} ARTVERSION";
//                         nexusArtifactUploader(
//                             nexusVersion: NEXUS_VERSION,
//                             protocol: NEXUS_PROTOCOL,
//                             nexusUrl: NEXUS_URL,
//                             groupId: NEXUS_REPOGRP_ID,
//                             version: ARTVERSION,
//                             repository: NEXUS_REPOSITORY,
//                             credentialsId: NEXUS_CREDENTIAL_ID,
//                             artifacts: [
//                                 [artifactId: pom.artifactId,
//                                 classifier: '',
//                                 file: artifactPath,
//                                 type: pom.packaging],
//                                 [artifactId: pom.artifactId,
//                                 classifier: '',
//                                 file: "pom.xml",
//                                 type: "pom"]
//                             ]
//                         );
//                     } 
// 		    else {
//                         error "*** File: ${artifactPath}, could not be found";
//                     }
//                 }
//             }
//         }


//     }


// }


def COLOR_MAP = [
    'SUCCESS': 'good',
    'UNSTABLE': 'warning',
    'FAILURE': 'danger'
]

pipeline {
    agent any

    tools {
        maven "MAVEN3"
        jdk "OracleJDK8"
    }
    
    environment {
        SNAP_REPO = 'vprofile-snapshot'
		NEXUS_USER = 'admin'
		NEXUS_PASS = 'Imagine123'
		RELEASE_REPO = 'vprofile-release'
		CENTRAL_REPO = 'vprofile-maven-central'
		NEXUSIP = '172.31.28.170'
		NEXUSPORT = '8081'
		NEXUS_GRP_REPO = 'vprofile-maven-group'
        NEXUS_LOGIN = 'nexus-login'
        SONARSERVER = 'sonar-pro'
        SONARSCANNER = 'sonar4.7'
    }

    stages {
        stage('Build'){
            steps {
                sh 'mvn -s settings.xml -DskipTests install'
            }

            post {
                success {
                    echo "Now Archiving...."
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }


        stage('Test'){
            steps {
                sh 'mvn -s settings.xml test'
            }

        }

        stage('Checkstyle Analysis'){
            steps {
                sh 'mvn -s settings.xml checkstyle:checkstyle'
            }
        }

        stage('Sonar Analysis') {
            environment {
                SCANNER_HOME = tool "${SONARSCANNER}"
            }

            steps {
               withSonarQubeEnv("${SONARSERVER}") {
                   sh '''${SCANNER_HOME}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                   -Dsonar.projectName=vprofile \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                   -Dsonar.junit.reportsPath=target/surefire-reports/ \
                   -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
              }
            }
        }

        stage("Quality Gate") {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    //* Parameter indicates whether to set pipeline to UNSTABLE if Quality Gate fails
                    //? true = set pipeline to UNSTABLE, false = don't
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage("UploadArtifact"){
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
                    [artifactId: 'vproapp',
                     classifier: '',
                     file: 'target/vprofile-v2.war',
                     type: 'war']
                  ]
                )
            }
        }

    }

    // post {
    //     always {
    //         echo 'Slack Notifications.'
    //         slackSend channel: '#jenkins-cicd',
    //             color: COLOR_MAP[currentBuild.currentResult],
    //             message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
    //     }
    // }

}

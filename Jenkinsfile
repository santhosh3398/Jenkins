pipeline {
    
	agent any
tools {
	    maven "Maven3.9.15"
	    jdk "java17"
	}
    
    stages{
        stage('checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/santhosh3398/Jenkins.git'
            }
        }
        stage('BUILD'){
            steps {
                sh 'mvn clean install -DskipTests'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.jar'
                }
            }
        }

	stage('UNIT TEST'){
            steps {
                sh 'mvn test'
            }
        }

	stage('INTEGRATION TEST'){
            steps {
                sh 'mvn verify -DskipUnitTests'
            }
        }
		
        stage ('CODE ANALYSIS WITH CHECKSTYLE'){
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
            post {
                success {
                    echo 'Generated Analysis Result'
                }
            }
        }

        stage('CODE ANALYSIS with SONARQUBE') {
          
		  environment {
             scannerHome = tool 'sonar8.0'
          }

          steps {
            withSonarQubeEnv('sonarserver') {
               sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=simple-java-app \
                   -Dsonar.projectName=simple-java-app \
                   -Dsonar.projectVersion=1.0 \
                   -Dsonar.sources=src/ \
                   -Dsonar.java.binaries=target/test-classes/com/example/ \
                   -Dsonar.junit.reportsPath=target/reports/ \
                   -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
            }

          }
        }
		stage('QUALITY GATE') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
		stage('publish to nexus') {
            steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: '172.31.3.97:8081',
                    repository: 'simplejava-release',
                    credentialsId: 'nexuslogin',
                    version: "${env.BUILD_ID}",
                     groupId: 'QA',
                     artifacts: [
                         [
                             artifactId: 'simple-java-app',
                             classifier: '',
                             file: 'target/simple-java-app-1.0.jar',
                             type: 'jar'
                         ]
                     ]
                     
                )
            }
        }
    }
}

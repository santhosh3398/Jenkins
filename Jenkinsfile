@Library('my-shared-library') _
pipeline {
    agent any
    tools {
        maven 'Maven3.9.15'
        jdk 'java17'
    }
    stages {
        stage('checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/santhosh3398/Jenkins.git'
            }
        }
        stage('build') {
            steps {
               mavenBuild()
            }
        }
        stage('post-build') {
            steps {
                echo 'Post-build actions are successful.'
            }
        }
    }
}

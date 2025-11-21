pipeline {
    agent any

    tools {
        jdk 'jdk17'
        maven 'maven3'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Deploy SNAPSHOT') {
            when {
                expression { return env.BRANCH_NAME == 'develop' }
            }
            steps {
                sh 'mvn deploy'
            }
        }

        stage('Release') {
            when {
                expression { return env.BRANCH_NAME == "main" }
            }
            steps {
                sh """
                    mvn versions:set -DremoveSnapshot
                    mvn clean deploy -P release
                    mvn versions:commit
                """
            }
        }
    }
}

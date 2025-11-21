pipeline {
    agent any

    tools {
        jdk 'jdk17'
        maven 'maven3'
    }
     // Toujours déclenché par GitHub webhook
        triggers {
            // Ceci déclenche le job sur tout push GitHub
            githubPush()
        }

    stages {
        /* stage('Checkout') {
            steps {
                checkout scm
            }
        } */
        stage('Checkout') {
                    steps {
                        // Forcer le checkout même si aucun changement détecté
                        checkout([$class: 'GitSCM',
                            branches: [[name: 'refs/heads/main']],
                            doGenerateSubmoduleConfigurations: false,
                            extensions: [[$class: 'WipeWorkspace']], // vide workspace à chaque build
                            userRemoteConfigs: [[url: 'https://github.com/ArfHassen/app-spring-demo.git']]
                        ])
                    }
                }

        stage('Build') {
            steps {
                sh 'mvn -s settings.xml clean package'
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
                    mvn -s settings.xml clean deploy -P release
                    mvn versions:commit
                """
            }
        }
    }
    post {
            always {
                echo 'Build terminé.'
            }
            success {
                echo 'Build réussi !'
            }
            failure {
                echo 'Build échoué.'
            }
        }
}

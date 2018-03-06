#!/usr/bin/env groovy
@Library('paas-pipeline-shared-libs@0.1.0') _
pipeline {
    agent { node { label 'ec2' } }

    stages {

        stage('Do not build if skip-build is in commit message') {
            steps {
                skipOrNotSkip()
            }
        }

        stage('Build pip package') {
            when {
                environment name: 'SKIP', value: 'false'
            }
            environment {
              REPO_URL = "https://gitlab.deveng.systems/paas/keymaker"
              AUTHOR = "PaaS Team"
              AUTHOR_EMAIL = "paas@argos.co.uk"
              TAG = getTagForCommit()
            }
            steps {
                //getTag()
                sh 'make build'
            }
        }

        stage('Upload to Nexus') {
            when {
                environment name: 'SKIP', value: 'false'
            }
            environment {
              TAG = getTagForCommit()
              NEXUS_URL = "https://deveng.io/nexus/content/repositories/"
              NEXUS_PATH = "releases/uk/co/argos/devops/pip"
            }
            steps {
              withCredentials([
                usernamePassword( credentialsId: 'nexus',
                usernameVariable: 'NEXUS_LOGIN', passwordVariable: 'NEXUS_PASSWORD')
                ]) {
                  sh 'make upload'
                }
            }
        }

    post {
        always {
            cleanWs()
        }
    }
}

// def getTag(){
//   env.TAG = getTagForCommit()
// }
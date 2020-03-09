#!/usr/bin/env groovy

pipeline {
    agent {
        label 'os:linux'
    }

    options {
        skipDefaultCheckout()
        buildDiscarder(logRotator(
            daysToKeepStr: '7'
        ))
    }

    environment {
        RETENTION_BUILD_SCRIPT_DIR = "${env.WORKSPACE}/retention"
        RETENTION_ARCHIVE_DIR = "${env.WORKSPACE}/retention-archive"
    }

    stages {
        stage('Clean Libs') {
            steps {
                 sh """
                    rm -rf *.tar.gz
                    rm -rf ${env.RETENTION_ARCHIVE_DIR}
                    mkdir -p  ${env.RETENTION_ARCHIVE_DIR}
                """
            }
        }

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Requirements') {
            steps {
                dir(env.RETENTION_BUILD_SCRIPT_DIR) {
                    sh """
                        pip3 install -r requirements.txt -t ${env.RETENTION_ARCHIVE_DIR}
                        cp -r ${env.RETENTION_BUILD_SCRIPT_DIR}/* ${env.RETENTION_ARCHIVE_DIR}
                    """
                }
            }
        }

        stage('Package') {
            steps {
                script {
                    def artifactName = artifactName(name: 'rentention', extension: "tar.gz")
                    sh "tar czf ${env.WORKSPACE}/${artifactName} ${env.RETENTION_ARCHIVE_DIR}/"
                }
            }
        }

        stage('Archive') {
            steps {
                archiveArtifacts artifacts: '*.tar.gz', onlyIfSuccessful: true
            }
        }
    }
}
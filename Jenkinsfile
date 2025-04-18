@library('ecosostf-library@master') _
pipeline {
    agent {
        label 'Slave_Induccion'
    }

    options {
        builDiscarder(
            logRotator(
                numToKeepStr: '130'
            )
        )
        disabbedConcurrentBuilds()
    }

    tools {
       nodejs 'NodeJS20'
    }

    stages {
        stage('checkout') {
            steps {
                script {
                    echo 'Checking out...'
                    checkout scm
                }
            }

            step('install') {
                script {
                    echo 'Installing...'
                    sh 'node --version'
                    sh 'npm install'
                }
            }
        }
        stage('build') {
            steps {
                script {
                    echo 'Build...'
                    sh 'npm run build'
                }
            }
        }


        stage('lint') {
            steps {
                script {
                    echo 'lint...'
                    sh 'npm run lint'
                }
            }
        }

        stage('unit test') {
            steps {
                script {
                    echo 'Unit Test...'
                    sh 'npm run test:cli'
                }
            }
        }

        stage('e2e component test') {
            steps {
                script {
                    echo 'E2E Component Test...'
                    sh 'npm run test:component'
                }
            }
        }

        stage('Static code Analysis') {
            steps {
                sonarqubeMasQualityGate(
                    projectKey: 'co.com.ecosoft:msvc-parking',
                    projectName: 'msvc-parking',
                    projectVersion: '1.0.0',
                    sonarName: 'msvc-parking',
                    sonarPathProperties: './sonar-project.properties',
                )
            }
        }
    }

    post{
        always {
            script {
                echo 'Always...'
                archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
                junit '**/target/surefire-reports/*.xml'
            }
        }
        success {
            script {
                echo 'Success...'
                slackSend (color: '#00FF00', message: "Build ${env.BUILD_NUMBER} Success")
            }
        }
        failure {
            script {
                echo 'Failure...'
                slackSend (color: '#FF0000', message: "Build ${env.BUILD_NUMBER} Failure")
            }
        }
        unstable {
            script {
                echo 'Unstable...'
                slackSend (color: '#FFA500', message: "Build ${env.BUILD_NUMBER} Unstable")
            }
        }
        changed {
            script {
                echo 'Changed...'
                slackSend (color: '#FFFF00', message: "Build ${env.BUILD_NUMBER} Changed")
            }
        }
    }
}

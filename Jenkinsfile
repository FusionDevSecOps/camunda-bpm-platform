pipeline {
    agent {
        node {
            label 'DevSecOps'
        }
    }
    options {
        skipDefaultCheckout true
        timeout(time: 3, unit: 'HOURS')
        timestamps()
        buildDiscarder(logRotator(numToKeepStr: '10', artifactNumToKeepStr: '10'))
    }
    stages {
        stage('Clean') {
            steps {
                echo 'Cleanup workspace'
                sh 'sudo chmod -fR 777 "${WORKSPACE}" && sudo rm -Rf ./*'
            }
        }
        stage('Checkout SCM') {
            steps {
                git 'https://github.com/FusionDevSecOps/camunda-bpm-platform.git'
            }
        }
        stage('Build') {
            steps {
                script {
                    sh 'mvn clean install -Dmaven.test.failure.ignore=true'
                }
            }
        }
         stage('Static code analysis, PMD ') {
            steps {
                script {
                    sh 'mvn jxr:jxr pmd:cpd'
                    sh 'mvn jxr:jxr pmd:pmd'

                }
            }
        }
        stage('Sonar') {
            steps {
                script {
                    sh 'mvn sonar:sonar -Dsonar.host.url=http://192.168.50.36:9000 -Dmaven.test.failure.ignore=true -Dsonar.login=admin -Dsonar.password=admin'
                }
            }
        }
    }
}
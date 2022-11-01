pipeline {
    agent any
    environment {
        DOCKERHUB_PASS = credentials('docker-pass')
    }
    stages {
        stage("Building the Student Survey Image") {
            steps {
                script {
                    checkout scm
                    sh 'rm -rf *.war'
                    sh 'jar -cvf SWE_645_kushaal.war -C WebContent/ .'
                    sh 'echo ${BUILD_TIMESTAMP}'
                    sh "docker login -u kushaaladdagatla -p ${DOCKERHUB_PASS}"
                    def customImage = docker.build("kushaaladdagatla/studentsurvey:${BUILD_TIMESTAMP}")
                }
            }
        }
        stage("Pushing Image to Dockerhub") {
            steps {
                script {
                    sh 'docker push kushaaladdagatla/studentsurvey:${BUILD_TIMESTAMP}'
                }
            }
        }
        stage("Deploying to Rancher as single pod") {
            steps {
                script {
                    sh 'kubectl set image deployment/studentsurvey-pipeline studentsurvey-pipeline=kushaaladdagatla/studentsurvey:${BUILD_TIMESTAMP} -n jenkins-pipeline'
                }
            }
        }
        stage("Deploying to Rancher as with load balancer") {
            steps {
                script {
                    sh 'kubectl set image deployment/studentsurvey-lb studentsurvey-lb=kushaaladdagatla/studentsurvey:${BUILD_TIMESTAMP} -n jenkins-pipeline'
                }
            }
        }
    }
}

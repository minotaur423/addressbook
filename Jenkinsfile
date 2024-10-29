pipeline{
    agent any
    tools{
        maven 'maven'
    }
    options {
        buildDiscarder logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '30', numToKeepStr: '10')
    }
    environment{
        dockerhub_cred = credentials('docker_cred')
    }
    stages{
        stage('Clean Workspace'){
            steps{
                sh 'docker system prune -af'
            }
        }
        stage('Checkout stage'){
            steps{
                checkout scmGit(branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/minotaur423/addressbook.git']])
            }
        }
        stage('Maven Build'){
            steps{
                sh 'mvn package'
            }
        }
        stage('docker build'){
            steps{
                sh "docker build -t ${ARTIFACTORY_DOCKER_SERVER}/docker/project-docker:${BUILD_NUMBER} ."
            }
        }
        stage('Artifactory push'){
            steps{
                sh 'echo $dockerhub_cred_PSW | docker login -u $dockerhub_cred_USR ${ARTIFACTORY_DOCKER_SERVER} --password-stdin'
                sh "docker push ${ARTIFACTORY_DOCKER_SERVER}/docker/project-docker:${BUILD_NUMBER}"
            }
        }
/*        stage('Docker run'){
            steps{
                sh "docker run -d -p 80:8080 --name addressbook ${ARTIFACTORY_DOCKER_SERVER}/docker/project-docker:${BUILD_NUMBER}"
            }
        }*/
    }
    post {
        always {
            echo "job is completed"
            sh "docker logout"
        }
        success {
            echo "It is a success"
        }
        failure {
            echo "It has failed"
         }
    }
}

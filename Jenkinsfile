node {
    checkout scm
    def dockerfile = 'Dockerfile'
    def customImage = docker.build("docker-jenkins-pipeline:${env.BUILD_ID}", "-f ${dockerfile} .")
}

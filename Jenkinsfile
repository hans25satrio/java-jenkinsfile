pipeline {
    agent any
    stages {
        stage('Build') { 
            steps {
                sh 'rm -rf ./app'
                sh 'mkdir app'
                sh 'rm -rf ./target'
                sh 'mvn -B -DskipTests clean package'
                sh 'docker build --tag java-jenkinsfile:development .'
                sh 'docker save -o app/java-jenkinsfile.tar java-jenkinsfile:development'
                sh 'docker rmi java-jenkinsfile:development'
                sh 'rm -rf ./target'
            }
        }
        stage('Deploy') {
            steps {
                sh 'ssh -o StrictHostKeyChecking=no ubuntu@${your_application_server_ip} rm -rf /home/ubuntu/app'
                sh 'ssh -o StrictHostKeyChecking=no ubuntu@${your_application_server_ip} mkdir -p /home/ubuntu/app'
                sh 'scp -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no -r target/java-jenkinsfile.tar ubuntu@${your_application_server_ip}:/home/ubuntu/app'
                sh 'ssh -o StrictHostKeyChecking=no ubuntu@${your_application_server_ip} docker container rm -f java-jenkinsfile'
                sh 'ssh -o StrictHostKeyChecking=no ubuntu@${your_application_server_ip} docker rmi java-jenkinsfile:development -f || true'
                sh 'ssh -o StrictHostKeyChecking=no ubuntu@${your_application_server_ip} docker load --input /home/ubuntu/app/java-jenkinsfile.tar'
                sh 'ssh -o StrictHostKeyChecking=no ubuntu@${your_application_server_ip} docker run -ti -d --name java-jenkinsfile -p 8080:8080 java-jenkinsfile:development'
            }
        }
    }
}
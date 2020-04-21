pipeline {
    agent any
    stages {
        stage('Build') { 
            steps {
                sh 'mkdir app'
                sh 'mvn -B -DskipTests clean package'
                sh 'docker build --tag java-jenkinsfile:development .'
                sh 'docker save -o app/java-jenkinsfile.tar java-jenkinsfile:development'
                sh 'docker rmi java-jenkinsfile:development'
                sh 'rm -rf ./target'
            }
        }
        stage('Deploy') {
            steps {
                sh 'ssh -o StrictHostKeyChecking=no ubuntu@52.79.206.115 rm -rf /home/ubuntu/app'
                sh 'ssh -o StrictHostKeyChecking=no ubuntu@52.79.206.115 mkdir -p /home/ubuntu/app'
                sh 'scp -o UserKnownHostsFile=/dev/null -o StrictHostKeyChecking=no -r target/java-jenkinsfile.tar ubuntu@52.79.206.115:/home/ubuntu/app'
                sh 'rm -rf ./app'
                sh 'ssh -o StrictHostKeyChecking=no ubuntu@52.79.206.115 docker container rm -f java-jenkinsfile'
                sh 'ssh -o StrictHostKeyChecking=no ubuntu@52.79.206.115 docker rmi java-jenkinsfile:development -f || true'
                sh 'ssh -o StrictHostKeyChecking=no ubuntu@52.79.206.115 docker load --input /home/ubuntu/app/java-jenkinsfile.tar'
                sh 'ssh -o StrictHostKeyChecking=no ubuntu@52.79.206.115 docker run -ti -d --name java-jenkinsfile -p 8080:8080 java-jenkinsfile:development'
            }
        }
    }
}
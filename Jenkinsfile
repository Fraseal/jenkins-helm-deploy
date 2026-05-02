pipeline {
    agent any
    stages {
        stage('Build Maven') {
            steps {
                sh 'pwd'    // Print the current working directory
                sh 'mvn clean install package' // Run Maven clean, install, and package commands
            }
        }
        stage ('Copy Artifacts') {
            steps {
                sh 'pwd'
                sh 'cp -r target/*.jar docker' //cp generated jar files from target to docker registry
            }
        }
        stage('Unit Tests') {
            steps {
                sh 'mvn test'  // for test 
            }
        }
        stage('Build Docker Image'){
            steps{
                script {
                    def customImage = docker.build("aj085/petclinic:${env.BUILD_NUMBER}", "./docker")
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                    customImage.push()    
                }
            }
        }
    }
    stage('Build on kubernetes'){
        steps {
            withKubeConfig([credentialsId: 'kubeconfig']) {
                sh 'pwd'
                sh 'cp -R helm/* .'
                sh 'ls -ltrh'
                sh 'pwd'
                sh '/usr/local/bin/helm upgrade --install petclinic-app petclinic --set image.repository=aj085/petclinic --set image.tag=${BUILD_NUMBER}'
        }
    }
}

}
}

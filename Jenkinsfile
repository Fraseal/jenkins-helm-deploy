pipeline {
    agent any
    stages {
        stage('Build Maven') {
            steps {
                sh 'pwd'
                sh 'mvn clean install package'
            }
        }

        stage('Copy Artifacts') {
            steps {
                sh 'pwd'
                sh 'cp -r target/*.jar docker'
            }
        }

        stage('Unit Tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def customImage = docker.build("aj085/petclinic:${env.BUILD_NUMBER}", "./docker")
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        customImage.push()
                    }
                }
            }
        }

    }
}

        stage('Build on kubernetes'){    // Deploying app on kubernetes using helm
        steps {
            withKubeConfig([credentialsId: 'kubeconfig']) {  // Using kubeconfig credentials to access kubernetes cluster
                sh 'pwd'
                sh 'cp -R helm/* .' // Copying helm chart files to workspace
                sh 'ls -ltrh'
                sh 'pwd'
                sh '/usr/local/bin/helm upgrade --install petclinic-app petclinic --set image.repository=aj085/petclinic --set image.tag=${BUILD_NUMBER}'
        }                     // Using helm upgrade command to deploy the application on kubernetes cluster, setting the image repository and tag to the one we just built and pushed to docker hub
    }
}

}
}